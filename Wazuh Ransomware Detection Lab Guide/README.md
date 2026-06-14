# 🛡️ Wazuh Ransomware Detection Lab

> Real-time ransomware detection and automated response using **Wazuh All-in-One** + **Ubuntu 24.04 LTS** agent — with safe simulation testing included.

![Wazuh](https://img.shields.io/badge/Wazuh-4.x-3585F9?style=for-the-badge&logo=wazuh&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04_LTS-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-2DA4AA?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-2DA4AA?style=for-the-badge)

---

## 📌 What is this?

This repository documents a complete **ransomware detection lab** built on Wazuh. It walks through every step — from agent installation to live alert monitoring — and includes safe simulation scripts to test your detection rules without touching real data.

**Use this if you want to:**
- Build a working Wazuh ransomware detection setup from scratch
- Understand how FIM, Auditd, and custom rules work together
- Test and validate detection before a real incident happens
- Train your team or run a security workshop demo

---

## 🏗️ Lab Architecture

```
                        WAZUH RANSOMWARE DETECTION LAB
 ───────────────────────────────────────────────────────────────────────────────

  ┌─────────────────────────────────────┐        ┌─────────────────────────────────────┐
  │         UBUNTU 24.04 LTS            │        │         WAZUH ALL-IN-ONE            │
  │             Agent                   │        │              Server                 │
  │                                     │        │                                     │
  │  ┌───────────────────────────────┐  │        │  ┌───────────────────────────────┐  │
  │  │  FIM — syscheck               │  │        │  │  Wazuh Manager                │  │
  │  │  · realtime file monitoring   │  │        │  │  · Rule engine                │  │
  │  │  · extension detection        │  │        │  │  · Custom rules 100200–100204 │  │
  │  │  · ransom note detection      │  │        │  │  · Alert correlation          │  │
  │  └───────────────────────────────┘  │        │  └───────────────────────────────┘  │
  │                                     │        │                                     │
  │  ┌───────────────────────────────┐  │TCP 1514│  ┌───────────────────────────────┐  │
  │  │  Auditd                       │  │───────►│  │  Wazuh Indexer                │  │
  │  │  · rename / truncate watch    │  │        │  │  · Event storage              │  │
  │  │  · chmod mass detection       │  │        │  │  · Alert indexing             │  │
  │  │  · syscall-level capture      │  │        │  └───────────────────────────────┘  │
  │  └───────────────────────────────┘  │        │                                     │
  │                                     │        │  ┌───────────────────────────────┐  │
  │  ┌───────────────────────────────┐  │        │  │  Wazuh Dashboard  (HTTPS 443) │  │
  │  │  Active Response              │◄─┼────────┤  │  · Real-time alert view       │  │
  │  │  · kill-process.sh            │  │        │  │  · Security events            │  │
  │  │  · firewall-drop (iptables)   │  │        │  │  · MITRE ATT&CK mapping       │  │
  │  └───────────────────────────────┘  │        │  └───────────────────────────────┘  │
  │                                     │        └─────────────────────────────────────┘
  │  ┌───────────────────────────────┐  │                 <Wazuh_Manager_IP>
  │  │  Simulation (safe)            │  │
  │  │  · ransom_sim.py              │  │
  │  │  · /tmp/ransom_test only      │  │
  │  └───────────────────────────────┘  │
  └─────────────────────────────────────┘
              <Agent_IP>

 ───────────────────────────────────────────────────────────────────────────────
  Detection flow:  File event → FIM / Auditd → Rule match → Alert → AR fires
```

---

## ⚡ Detection Layers

| Layer | Technology | What it detects |
|---|---|---|
| **FIM** | Wazuh syscheck | Mass file renames, new `.locked`/`.encrypted` files, ransom notes |
| **Syscall** | Auditd | Mass `rename()`, `truncate()`, `chmod()` calls at kernel level |
| **Custom rules** | Wazuh rule engine | Extension patterns, frequency thresholds, ransom note filenames |
| **Auto-response** | Active Response | Kill offending process, block network via iptables |

---

## 📋 Requirements

- Wazuh All-in-One server (v4.x) — manager + indexer + dashboard
- Ubuntu 24.04 LTS — for the monitored agent
- Network connectivity between agent and manager on **TCP port 1514**
- Root or sudo access on both machines

---

## 🔧 IP Variables

All configuration examples use placeholders. Replace every value before applying:

| Variable | Example | Description |
|---|---|---|
| `<Wazuh_Manager_IP>` | `192.168.1.10` | IP of your Wazuh all-in-one server |
| `<Agent_IP>` | `192.168.1.50` | IP of the Ubuntu 24.04 agent |
| `<Agent_Name>` | `ubuntu-agent-01` | Agent hostname on the manager |
| `<Agent_Group>` | `linux-servers` | Policy group for this agent |
| `<Manager_Port>` | `1514` | Agent-to-manager TCP port |
| `<Dashboard_Port>` | `443` | Wazuh dashboard HTTPS port |

---

## 🚀 Quick Start

### Step 1 — Install Wazuh agent on Ubuntu 24.04

```bash
# Add GPG key and repository
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | \
  gpg --no-default-keyring \
  --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import

chmod 644 /usr/share/keyrings/wazuh.gpg

echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] \
  https://packages.wazuh.com/4.x/apt/ stable main" | \
  tee /etc/apt/sources.list.d/wazuh.list

apt-get update

# Install — replace <Wazuh_Manager_IP> with your actual IP
WAZUH_MANAGER="<Wazuh_Manager_IP>" \
  WAZUH_AGENT_NAME="<Agent_Name>" \
  apt-get install -y wazuh-agent

systemctl enable wazuh-agent --now
```

### Step 2 — Enable FIM on the agent

Add to `/var/ossec/etc/ossec.conf` on the agent:

```xml
<syscheck>
  <disabled>no</disabled>
  <frequency>60</frequency>
  <directories check_all="yes" realtime="yes" report_changes="yes">/home</directories>
  <directories check_all="yes" realtime="yes" report_changes="yes">/tmp/ransom_test</directories>
  <alert_new_files>yes</alert_new_files>
</syscheck>
```

### Step 3 — Add Auditd rules on the agent

```bash
sudo apt install auditd audispd-plugins -y
```

Create `/etc/audit/rules.d/ransomware.rules`:

```bash
-a always,exit -F arch=b64 -S rename -S renameat -k ransomware_rename
-a always,exit -F arch=b64 -S truncate -S ftruncate -k ransomware_truncate
-w /home -p wxa -k home_dir_watch
-w /tmp  -p wxa -k tmp_dir_watch
```

```bash
sudo augenrules --load && sudo systemctl enable auditd --now
```

### Step 4 — Deploy custom detection rules on the manager

Create `/var/ossec/etc/rules/ransomware_detection.xml`:

```xml
<group name="ransomware,">

  <rule id="100200" level="12">
    <if_group>syscheck</if_group>
    <field name="file">.locked$|.encrypted$|.crypt$|.enc$|.WNCRY$</field>
    <description>Ransomware: suspicious extension detected - $(file)</description>
    <mitre><id>T1486</id></mitre>
  </rule>

  <rule id="100201" level="14" frequency="10" timeframe="30">
    <if_matched_group>syscheck</if_matched_group>
    <same_field>agent.id</same_field>
    <description>Ransomware: mass file modification (10+ files in 30s)</description>
    <mitre><id>T1486</id></mitre>
  </rule>

  <rule id="100202" level="12">
    <if_sid>554</if_sid>
    <field name="file">.locked$|.encrypted$|.enc$|.crypt$</field>
    <description>Ransomware: new encrypted file created - $(file)</description>
    <mitre><id>T1486</id></mitre>
  </rule>

  <rule id="100203" level="15">
    <if_group>syscheck</if_group>
    <field name="file">README_DECRYPT|HOW_TO_DECRYPT|RANSOM_NOTE</field>
    <description>Ransomware: ransom note detected - $(file)</description>
    <mitre><id>T1486</id></mitre>
  </rule>

  <rule id="100204" level="13" frequency="20" timeframe="30">
    <if_matched_sid>80790</if_matched_sid>
    <field name="audit.key">ransomware_rename</field>
    <description>Ransomware: mass file rename via syscall</description>
    <mitre><id>T1486</id></mitre>
  </rule>

</group>
```

### Step 5 — Configure Active Response on the manager

> ⚠️ **Important:** Every command name in `<active-response>` requires a matching `<command>` definition block. Missing it causes **error 1303** and the manager will not start.

Add to `/var/ossec/etc/ossec.conf`:

```xml
<!-- COMMAND DEFINITIONS — required for every AR command used -->
<command>
  <name>firewall-drop</name>
  <executable>firewall-drop</executable>
  <timeout_allowed>yes</timeout_allowed>
</command>

<command>
  <name>kill-process</name>
  <executable>kill-process.sh</executable>
  <timeout_allowed>yes</timeout_allowed>
</command>

<!-- ACTIVE RESPONSE TRIGGERS -->
<active-response>
  <command>firewall-drop</command>
  <location>local</location>
  <rules_id>100200,100201,100202,100203,100204</rules_id>
  <level>12</level>
  <timeout>3600</timeout>
</active-response>

<active-response>
  <command>kill-process</command>
  <location>local</location>
  <rules_id>100200,100201,100202,100203,100204</rules_id>
  <level>12</level>
  <timeout>60</timeout>
</active-response>
```

Validate and restart:

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
sudo systemctl restart wazuh-manager
```

---

## 🧪 Simulation Testing

> ✅ All tests run inside `/tmp/ransom_test` only — no real data is affected.

### Python simulator (recommended)

```bash
# Save as /tmp/ransom_sim.py on the agent, then run:
python3 /tmp/ransom_sim.py

# Cleanup after test
python3 /tmp/ransom_sim.py cleanup
```

### Quick shell test

```bash
mkdir -p /tmp/ransom_test

for i in $(seq 1 20); do
  echo "data $i" > /tmp/ransom_test/file_$i.txt
done

for f in /tmp/ransom_test/*.txt; do
  mv "$f" "${f%.txt}.locked"
  sleep 0.2
done

echo "RANSOM" > /tmp/ransom_test/README_DECRYPT.txt

# Cleanup
rm -rf /tmp/ransom_test
```

---

## 📡 Monitor Alerts in Real Time

Open on the **Wazuh manager** before running any simulation:

```bash
# All alerts live
sudo tail -f /var/ossec/logs/alerts/alerts.json | python3 -m json.tool

# Ransomware rules only
sudo tail -f /var/ossec/logs/alerts/alerts.json | python3 -c "
import sys, json
RULES = {'100200','100201','100202','100203','100204'}
for line in sys.stdin:
    try:
        a = json.loads(line)
        if str(a.get('rule',{}).get('id','')) in RULES:
            print(f\"[ALERT] Rule {a['rule']['id']} — {a['rule']['description']}\")
    except: pass
"

# Active response log
sudo tail -f /var/ossec/logs/active-responses.log
```

Dashboard: `https://<Wazuh_Dashboard_IP>` → Security Events → filter `rule.groups: ransomware`

---

## 🔍 Detection Rules Summary

| Rule ID | Level | Trigger | MITRE |
|---|---|---|---|
| `100200` | 12 | File with `.locked`, `.encrypted`, `.enc`, `.crypt`, `.WNCRY` extension | T1486 |
| `100201` | 14 | 10+ file modifications within 30 seconds | T1486 |
| `100202` | 12 | New encrypted-extension file created | T1486 |
| `100203` | **15** | Ransom note file detected (`README_DECRYPT`, etc.) | T1486 |
| `100204` | 13 | 20+ file rename syscalls within 30 seconds (auditd) | T1486 |

---

## 🛠️ Troubleshooting

| Error | Cause | Fix |
|---|---|---|
| `Error 1303: Invalid command` | `<command>` definition missing for AR command | Add `<command>` block for every name used in `<active-response>` |
| `Error 1202: Configuration error` | XML or logic error in `ossec.conf` | Run `/var/ossec/bin/wazuh-analysisd -t` |
| Manager fails to start | CRITICAL error in analysisd | Check `journalctl -xeu wazuh-manager.service` |
| Agent shows Disconnected | Port 1514 blocked | Open TCP 1514 from `<Agent_IP>` to `<Wazuh_Manager_IP>` |
| No alerts in dashboard | FIM disabled or wrong path | Verify `<syscheck>` block; set `realtime="yes"` |
| Active response not firing | Wrong rule ID or level in AR config | Match `rules_id` and `level` to your custom rules |
| `kill-process.sh` not executing | Wrong permissions | `chmod 750` and `chown root:wazuh` on the script |

---

## 📚 Resources

- [Wazuh Official Documentation](https://documentation.wazuh.com)
- [MITRE ATT&CK T1486 — Data Encrypted for Impact](https://attack.mitre.org/techniques/T1486/)
- [Wazuh Active Response Guide](https://documentation.wazuh.com/current/user-manual/capabilities/active-response/)
- [Wazuh FIM Documentation](https://documentation.wazuh.com/current/user-manual/capabilities/file-integrity/)

---

<div align="center">

**Built for the security community** · Powered by [Wazuh](https://wazuh.com)

*All Wazuh trademarks and logos are property of Wazuh Inc.*

</div>
