<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Wazuh_Logo.svg/320px-Wazuh_Logo.svg.png" alt="Wazuh" width="280"/>
</p>

<h1 align="center">wazuh-community-hub</h1>

<p align="center">
  <b>The open-source community knowledge base for Wazuh defenders.</b><br/>
  Detection rules · SOC simulations · Integrations · Compliance labs · Tutorials · 5.0 Migration
</p>

<p align="center">
  <a href="https://github.com/AramEvin/wazuh-community-hub/stargazers">
    <img src="https://img.shields.io/github/stars/AramEvin/wazuh-community-hub?style=for-the-badge&color=3585F9&labelColor=000000&logo=github" alt="Stars"/>
  </a>
  <a href="https://github.com/AramEvin/wazuh-community-hub/network/members">
    <img src="https://img.shields.io/github/forks/AramEvin/wazuh-community-hub?style=for-the-badge&color=3585F9&labelColor=000000&logo=github" alt="Forks"/>
  </a>
  <a href="https://github.com/AramEvin/wazuh-community-hub/graphs/contributors">
    <img src="https://img.shields.io/github/contributors/AramEvin/wazuh-community-hub?style=for-the-badge&color=FEDD0B&labelColor=000000&logo=github" alt="Contributors"/>
  </a>
  <a href="./LICENSE">
    <img src="https://img.shields.io/badge/License-MIT-ffffff?style=for-the-badge&labelColor=000000" alt="MIT License"/>
  </a>
  <a href="https://github.com/AramEvin/wazuh-community-hub/pulls">
    <img src="https://img.shields.io/badge/PRs-Welcome-22c55e?style=for-the-badge&labelColor=000000" alt="PRs Welcome"/>
  </a>
</p>

<p align="center">
  <a href="https://wazuh.com/community/ambassadors/">
    <img src="https://img.shields.io/badge/Wazuh-Ambassador-FEDD0B?style=for-the-badge&labelColor=000000" alt="Wazuh Ambassador"/>
  </a>
  <a href="https://documentation.wazuh.com/current/index.html">
    <img src="https://img.shields.io/badge/Wazuh-Docs-3585F9?style=for-the-badge&labelColor=000000" alt="Wazuh Docs"/>
  </a>
  <a href="https://wazuh.com/community/">
    <img src="https://img.shields.io/badge/Community-Slack-4A154B?style=for-the-badge&logo=slack&labelColor=000000" alt="Slack"/>
  </a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/SIEM-XDR-3585F9?style=flat-square&labelColor=111"/>
  <img src="https://img.shields.io/badge/MITRE%20ATT%26CK-Mapped-red?style=flat-square&labelColor=111"/>
  <img src="https://img.shields.io/badge/Linux-Windows-informational?style=flat-square&labelColor=111"/>
  <img src="https://img.shields.io/badge/AWS-Azure-GCP-orange?style=flat-square&labelColor=111"/>
  <img src="https://img.shields.io/badge/PCI--DSS-HIPAA-GDPR-green?style=flat-square&labelColor=111"/>
  <img src="https://img.shields.io/badge/Docker-Kubernetes-blue?style=flat-square&labelColor=111"/>
  <img src="https://img.shields.io/badge/Wazuh_5.0-Beta_Ready-FEDD0B?style=flat-square&labelColor=111"/>
</p>

---

> **This is not a mirror of official Wazuh documentation.**
> Every file here is original, community-built content — detection logic, integration recipes, attack simulations, migration guides, and walkthroughs built from real-world practice.
> Official docs live at [documentation.wazuh.com](https://documentation.wazuh.com/current/index.html).

---

## 📋 Table of Contents

- [Featured Content](#-featured-content)
- [Repository Structure](#️-repository-structure)
- [Quick Navigation](#-quick-navigation)
- [Detection Rules](#-detection-rules)
- [Integrations](#-integrations)
- [SOC Simulations](#-soc-simulations)
- [Compliance Labs](#-compliance-labs)
- [Official Wazuh Resources](#-official-wazuh-resources)
- [Contributing](#-contributing)
- [Community](#-community)
- [License](#-license)

---

## 🌟 Featured Content

### 🔴 Wazuh SOC Engineer Simulation Lab
**[`/soc-simulations/full-attack-chain/`](./soc-simulations/full-attack-chain/README.md)**

<img src="https://img.shields.io/badge/Wazuh-4.14.5-3585F9?style=flat-square&labelColor=000000"/>
<img src="https://img.shields.io/badge/Detection_Rate-13%2F13-22c55e?style=flat-square&labelColor=000000"/>
<img src="https://img.shields.io/badge/Attack_Phases-5-ef4444?style=flat-square&labelColor=000000"/>
<img src="https://img.shields.io/badge/Platform-Ubuntu_24.04_+_Kali-557C94?style=flat-square&labelColor=000000"/>

A full end-to-end attack and detection lab on Wazuh 4.14.5. Nine MITRE ATT&CK techniques executed across five phases — every single one detected with custom rules.

| Phase | Technique | MITRE | Level | Result |
|---|---|---|---|---|
| 1 — Recon | Nmap Network Scan | [T1046](https://attack.mitre.org/techniques/T1046/) | Info | ✅ |
| 2 — Initial Access | SSH Brute Force | [T1110.001](https://attack.mitre.org/techniques/T1110/001/) | 14 | ✅ |
| 3 — Lateral | SMB Enum + Pass-the-Hash | [T1135](https://attack.mitre.org/techniques/T1135/) / [T1550.002](https://attack.mitre.org/techniques/T1550/002/) | 10 | ✅ |
| 4 — Privilege | Account Creation + Shadow Access | [T1136.001](https://attack.mitre.org/techniques/T1136/001/) / [T1003.008](https://attack.mitre.org/techniques/T1003/008/) | 13 | ✅ |
| 5 — Persist | SSH Backdoor + Crontab + Staging | [T1098.004](https://attack.mitre.org/techniques/T1098/004/) / [T1053.003](https://attack.mitre.org/techniques/T1053/003/) | 13 | ✅ |

> 13 custom rules · 1,070 events detected · 100% detection rate · Under 30s detection latency
> Includes: full rule XML, audit configs, lab cleanup script, dashboard queries, compliance mapping

→ **[View Full Lab](./soc-simulations/full-attack-chain/README.md)**

---

### 🟡 Wazuh 4.14 → 5.0 Beta — Architecture Deep Dive
**[`/wazuh-5-beta/`](./wazuh-5-beta/README.md)**

<img src="https://img.shields.io/badge/Wazuh_5.0-Beta_April_2026-FEDD0B?style=flat-square&labelColor=000000"/>
<img src="https://img.shields.io/badge/Breaking_Points-15-ef4444?style=flat-square&labelColor=000000"/>
<img src="https://img.shields.io/badge/Migration-Blue--Green-3585F9?style=flat-square&labelColor=000000"/>

The most significant architecture shift in Wazuh history. Filebeat is gone. analysisd is rewritten in C++. eBPF replaces auditd. 15 breaking changes to validate before cutover.

| What changed | 4.14 | 5.0 Beta |
|---|---|---|
| Ingestion pipeline | Filebeat → Indexer | indexer-connector (in-process) |
| Event processor | analysisd (C) | Engine (C++, parallel workers) |
| FIM who-data | auditd dependency | eBPF (< 10ms latency, no auditd) |
| Threat intel | wazuh-integratord (per-alert API) | Native CTI (in-stream, microseconds) |
| Cluster | Optional (`cluster.disabled`) | Always on — option removed |
| Manager root path | `/var/ossec/` | `/var/wazuh-manager/` |

> ⚠️ Based on Beta 1 (April 2026). Validate against official docs before applying to production.

→ **[View Architecture Guide](./wazuh-5-beta/README.md)**

---

## 🗂️ Repository Structure

```
wazuh-community-hub/
│
├── 📁 soc-simulations/               # Attack simulations + Wazuh detections
│   ├── full-attack-chain/            # ★ 9-technique lab, 13 rules, 100% detection
│   ├── brute-force/
│   ├── ransomware-behavior/
│   ├── lateral-movement/
│   └── README.md
│
├── 📁 wazuh-5-beta/                  # ★ 4.14→5.0 architecture + 15 breaking points
│   └── README.md
│
├── 📁 detection-rules/               # Original custom Wazuh XML rules
│   ├── linux/
│   ├── windows/
│   ├── cloud/
│   └── README.md
│
├── 📁 integrations/                  # Step-by-step integration guides
│   ├── slack/
│   ├── thehive/
│   ├── shuffle/
│   ├── virustotal/
│   ├── misp/
│   └── README.md
│
├── 📁 compliance-labs/               # Wazuh mapped to compliance controls
│   ├── pci-dss/
│   ├── hipaa/
│   ├── gdpr/
│   ├── nist/
│   └── README.md
│
├── 📁 tutorials/                     # Original walkthroughs by skill level
│   ├── beginner/
│   ├── intermediate/
│   ├── advanced/
│   └── README.md
│
├── 📁 dashboards/                    # Custom dashboard JSON exports
├── 📁 scripts/                       # Deployment and automation helpers
└── 📁 resources/                     # Cheat sheets, diagrams, reference cards
```

---

## ⚡ Quick Navigation

| I want to… | Go here |
|---|---|
| 🔴 Run a full SOC attack & detect lab | [`/soc-simulations/full-attack-chain`](./soc-simulations/full-attack-chain/) |
| 🟡 Understand Wazuh 5.0 changes | [`/wazuh-5-beta`](./wazuh-5-beta/) |
| 🆕 Install and configure Wazuh | [`/tutorials/beginner`](./tutorials/beginner/) |
| 🧠 Deploy custom detection rules | [`/detection-rules`](./detection-rules/) |
| 🔗 Connect Wazuh to Slack / TheHive / MISP | [`/integrations`](./integrations/) |
| 📋 Build compliance evidence with Wazuh | [`/compliance-labs`](./compliance-labs/) |
| 📊 Import custom dashboards | [`/dashboards`](./dashboards/) |
| 🛠️ Automate agent deployment | [`/scripts`](./scripts/) |

---

## 🔴 Detection Rules

Original XML rules covering threat scenarios not in default Wazuh rulesets.

| Rule File | What It Detects | Platform | MITRE ATT&CK |
|---|---|---|---|
| `linux_cron_persistence.xml` | Non-root cron modifications — persistence indicator | Linux | [T1053.003](https://attack.mitre.org/techniques/T1053/003/) |
| `win_encoded_powershell.xml` | Base64-encoded PowerShell execution chains | Windows | [T1059.001](https://attack.mitre.org/techniques/T1059/001/) |
| `aws_s3_public_exposure.xml` | S3 bucket ACL changes granting public access | AWS | [T1530](https://attack.mitre.org/techniques/T1530/) |
| `docker_escape_attempt.xml` | Container escape via privileged process | Docker | [T1611](https://attack.mitre.org/techniques/T1611/) |
| `ssh_tunnel_detection.xml` | SSH port forwarding and tunneling | Linux | [T1572](https://attack.mitre.org/techniques/T1572/) |
| `win_lsass_access.xml` | LSASS memory access — credential theft indicator | Windows | [T1003.001](https://attack.mitre.org/techniques/T1003/001/) |
| `lab_custom_rules.xml` | Full SOC lab ruleset (100100–100399) | Linux | Multi-TTP |

**Rule ID ranges used in this repo:**

| Range | Category |
|---|---|
| `100001 – 100999` | Linux endpoint rules |
| `101000 – 101999` | Windows endpoint rules |
| `102000 – 102999` | Cloud (AWS / Azure / GCP) |
| `103000 – 103999` | Container / Kubernetes |
| `104000 – 104999` | Application / web server |

---

## 🔗 Integrations

| Integration | What It Does | Guide |
|---|---|---|
| **Slack** | Severity-based alert routing with custom message format | [`/integrations/slack`](./integrations/slack/) |
| **TheHive** | Auto case creation from critical/high Wazuh alerts | [`/integrations/thehive`](./integrations/thehive/) |
| **Shuffle** | SOAR playbook triggers mapped to Wazuh rule groups | [`/integrations/shuffle`](./integrations/shuffle/) |
| **VirusTotal** | FIM hash enrichment on new or changed files | [`/integrations/virustotal`](./integrations/virustotal/) |
| **MISP** | Threat intel correlation with live Wazuh events | [`/integrations/misp`](./integrations/misp/) |

---

## 🧪 SOC Simulations

| Simulation | Difficulty | MITRE | Active Response |
|---|---|---|---|
| [Full Attack Chain — 9 TTPs](./soc-simulations/full-attack-chain/) | 🔴 Full Lab | Multi | ✅ |
| SSH Brute Force → Detection + Block | 🟢 Beginner | [T1110.001](https://attack.mitre.org/techniques/T1110/001/) | ✅ |
| Mimikatz Credential Dump | 🟡 Intermediate | [T1003.001](https://attack.mitre.org/techniques/T1003/001/) | ✅ |
| Ransomware File Behavior | 🟡 Intermediate | [T1486](https://attack.mitre.org/techniques/T1486/) | ✅ |
| Lateral Movement via PsExec | 🔴 Advanced | [T1570](https://attack.mitre.org/techniques/T1570/) | ⚙️ |
| C2 Beacon via DNS Tunneling | 🔴 Advanced | [T1071.004](https://attack.mitre.org/techniques/T1071/004/) | ⚙️ |

---

## 📋 Compliance Labs

| Framework | Coverage |
|---|---|
| **PCI DSS** | File integrity, log retention, network monitoring — controls 10.2.4, 10.2.5, 11.5 |
| **HIPAA** | Audit log completeness, PHI system access — 164.312.c.1, 164.312.c.2 |
| **GDPR** | Data access alerting, breach detection — IV_32.2, IV_35.7.d |
| **NIST CSF** | Identify / Protect / Detect / Respond / Recover mapping |

---

## 📚 Official Wazuh Resources

| Resource | Link |
|---|---|
| 📖 Full Documentation | [documentation.wazuh.com](https://documentation.wazuh.com/current/index.html) |
| 🚀 Installation Guide | [Installation](https://documentation.wazuh.com/current/installation-guide/index.html) |
| ⚙️ Agent Enrollment | [Agent Deployment](https://documentation.wazuh.com/current/user-manual/agent/agent-enrollment/index.html) |
| 📝 Custom Rules | [Writing Rules](https://documentation.wazuh.com/current/user-manual/ruleset/custom.html) |
| 🔁 Active Response | [Active Response](https://documentation.wazuh.com/current/user-manual/capabilities/active-response/index.html) |
| ☁️ Cloud Security | [Cloud Monitoring](https://documentation.wazuh.com/current/cloud-security/index.html) |
| 🛡️ Compliance | [Regulatory Compliance](https://documentation.wazuh.com/current/compliance/index.html) |
| 🔗 Integrations | [Third-party Integrations](https://documentation.wazuh.com/current/user-manual/manager/manual-integration.html) |
| 📦 FIM | [File Integrity Monitoring](https://documentation.wazuh.com/current/user-manual/capabilities/file-integrity/index.html) |
| 🐛 GitHub | [wazuh/wazuh](https://github.com/wazuh/wazuh) |
| 💬 Community | [Join Slack](https://wazuh.com/community/) |
| 🐦 X / Twitter | [@wazuh](https://twitter.com/wazuh) |

---

## 🤝 Contributing

All levels welcome — from a typo fix to a full simulation lab.

```bash
# 1. Fork and clone
git clone https://github.com/AramEvin/wazuh-community-hub.git

# 2. Create a feature branch
git checkout -b feat/your-contribution-name

# 3. Add content following the folder structure
# 4. Open a pull request with a clear description
```

**Guidelines**
- Original content only — do not copy from official Wazuh docs
- Test rules before submitting — include the test command in the comment header
- Comment every config — explain what and why, not just how
- Include a Prerequisites section for intermediate/advanced content
- Respect [Wazuh Brand Guidelines](https://wazuh.com) when using brand assets

**Content we're looking for**
- Rules targeting specific CVEs or active malware families
- Azure AD / GCP audit log detection scenarios
- Wazuh + Kubernetes runtime threat detection
- Non-English tutorials (Arabic, Spanish, French, Turkish, etc.)
- Wazuh on edge / OT / ICS environments

---

## 🌐 Community

Part of the **Wazuh Ambassador Program** — a global effort to grow open-source security education.

<p align="center">
  <a href="https://wazuh.com/community/">
    <img src="https://img.shields.io/badge/Wazuh%20Community-Join%20Slack-4A154B?style=for-the-badge&logo=slack&labelColor=000000"/>
  </a>
  <a href="https://twitter.com/wazuh">
    <img src="https://img.shields.io/badge/Follow-@wazuh-1DA1F2?style=for-the-badge&logo=twitter&labelColor=000000"/>
  </a>
  <a href="https://www.linkedin.com/company/wazuh">
    <img src="https://img.shields.io/badge/LinkedIn-Wazuh-0A66C2?style=for-the-badge&logo=linkedin&labelColor=000000"/>
  </a>
  <a href="https://wazuh.com">
    <img src="https://img.shields.io/badge/Website-wazuh.com-3585F9?style=for-the-badge&labelColor=000000"/>
  </a>
</p>

---

## License

Original content is licensed under [MIT](./LICENSE).

> **Wazuh®** is a registered trademark of Wazuh, Inc.
> This is an independent community project, not officially affiliated with or endorsed by Wazuh, Inc.
> Brand assets used in compliance with the [Wazuh Branding and Trademark Guidelines](https://wazuh.com).

---

<p align="center">
  Made with ❤️ for the open-source security community<br/>
  <b>wazuh-community-hub</b> · Maintained by <a href="https://github.com/AramEvin">AramEvin</a> — Wazuh Ambassador
</p>
