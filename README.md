<div align="center">

```
                    ██╗    ██╗ █████╗ ███████╗██╗   ██╗██╗  ██╗
                    ██║    ██║██╔══██╗╚══███╔╝██║   ██║██║  ██║
                    ██║ █╗ ██║███████║  ███╔╝ ██║   ██║███████║
                    ██║███╗██║██╔══██║ ███╔╝  ██║   ██║██╔══██║
                    ╚███╔███╔╝██║  ██║███████╗╚██████╔╝██║  ██║
                     ╚══╝╚══╝ ╚═╝  ╚═╝╚══════╝ ╚═════╝ ╚═╝  ╚═╝
```

# wazuh-community-hub

**The community knowledge base for Wazuh defenders.**
Detection rules · Integrations · SOC labs · Compliance · Tutorials

[![Stars](https://img.shields.io/github/stars/AramEvin/wazuh-community-hub?style=flat&color=3585F9&labelColor=000000)](https://github.com/AramEvin/wazuh-community-hub/stargazers)
[![Contributors](https://img.shields.io/github/contributors/AramEvin/wazuh-community-hub?style=flat&color=FEDD0B&labelColor=000000)](https://github.com/AramEvin/wazuh-community-hub/graphs/contributors)
[![License: MIT](https://img.shields.io/badge/License-MIT-3585F9?style=flat&labelColor=000000)](./LICENSE)
[![Wazuh Ambassador](https://img.shields.io/badge/Wazuh-Ambassador-FEDD0B?style=flat&labelColor=000000)](https://wazuh.com/community/ambassadors/)
[![PRs Welcome](https://img.shields.io/badge/PRs-Welcome-22c55e?style=flat&labelColor=000000)](../../pulls)

---

*Built from scratch by the community · Not an official Wazuh product*

</div>

---

## What Lives Here?

> This repo is **not** a copy of official Wazuh documentation.
> Everything here is original, community-created content — real-world scenarios, custom detection ideas, hands-on labs, and integration recipes built from practice.

Whether you're a SOC analyst, a home lab builder, a security student, or an enterprise engineer — you'll find something practical here.

---

## 🗂 Repository Structure

```
wazuh-community-hub/
│
├── 📁 detection-rules/          # Original custom Wazuh XML rules
│   ├── linux/
│   ├── windows/
│   ├── cloud/
│   └── README.md
│
├── 📁 integrations/             # Step-by-step integration guides
│   ├── slack/
│   ├── thehive/
│   ├── shuffle/
│   ├── virustotal/
│   ├── misp/
│   └── README.md
│
├── 📁 soc-simulations/          # Attack simulations + expected Wazuh alerts
│   ├── brute-force/
│   ├── ransomware-behavior/
│   ├── lateral-movement/
│   └── README.md
│
├── 📁 compliance-labs/          # Compliance coverage with Wazuh
│   ├── pci-dss/
│   ├── hipaa/
│   ├── gdpr/
│   ├── nist/
│   └── README.md
│
├── 📁 tutorials/                # Written guides and walkthroughs
│   ├── beginner/
│   ├── intermediate/
│   ├── advanced/
│   └── README.md
│
├── 📁 dashboards/               # Custom dashboard JSON exports
│   └── README.md
│
├── 📁 scripts/                  # Deployment and automation helpers
│   └── README.md
│
└── 📁 resources/                # Cheat sheets, diagrams, reference cards
    └── README.md
```

---

## ⚡ Quick Navigation

| I want to… | Go here |
|---|---|
| 🆕 Set up Wazuh for the first time | [`/tutorials/beginner`](./tutorials/beginner/) |
| 🧠 Write custom detection rules | [`/detection-rules`](./detection-rules/) |
| 🔗 Connect Wazuh to Slack / TheHive / MISP | [`/integrations`](./integrations/) |
| 🧪 Simulate attacks and test detection | [`/soc-simulations`](./soc-simulations/) |
| 📋 Map Wazuh to PCI / HIPAA / GDPR | [`/compliance-labs`](./compliance-labs/) |
| 📊 Build better dashboards | [`/dashboards`](./dashboards/) |
| 🛠 Automate deployment | [`/scripts`](./scripts/) |

---

## 🔴 Detection Rules — Originals Only

Custom XML rules not found in the default Wazuh ruleset — written for real-world threat scenarios:

| Rule File | What It Detects | Platform | MITRE |
|---|---|---|---|
| `linux_cron_persistence.xml` | Suspicious cron job additions by non-root users | Linux | T1053.003 |
| `win_encoded_powershell.xml` | Base64-encoded PowerShell execution chains | Windows | T1059.001 |
| `aws_s3_public_exposure.xml` | S3 bucket ACL changes enabling public access | AWS | T1530 |
| `docker_escape_attempt.xml` | Container escape patterns via privileged commands | Docker | T1611 |
| `ssh_tunnel_detection.xml` | SSH port forwarding and tunneling behavior | Linux | T1572 |

Each rule ships with:
- A comment header explaining what it detects and why it matters
- A test command to trigger it in a lab environment
- Recommended active response pairing

**Rule ID convention used in this repo:**

| Range | Category |
|---|---|
| `100001–100999` | Linux endpoint rules |
| `101000–101999` | Windows endpoint rules |
| `102000–102999` | Cloud (AWS / Azure / GCP) rules |
| `103000–103999` | Container / Kubernetes rules |
| `104000–104999` | Application / web server rules |

---

## 🔗 Integrations — Working Configs

Real, tested configurations for extending Wazuh into your security stack:

- **Slack** — severity-based alert routing with custom message templates
- **TheHive** — automatic case creation from Wazuh critical/high alerts
- **Shuffle** — SOAR playbook triggers mapped to Wazuh rule groups
- **VirusTotal** — FIM hash enrichment on new or modified files
- **MISP** — threat intel feed correlation with live Wazuh events
- **Email** — HTML alert digest with rule context and remediation hints

Every guide includes: prerequisites → config snippets → how to verify it works.

---

## 🧪 SOC Simulations — Attack & Detect

Hands-on labs pairing attack techniques with the Wazuh alerts they produce:

| Simulation | Difficulty | MITRE Technique |
|---|---|---|
| SSH Brute Force → Detection + Auto-Block | 🟢 Beginner | T1110.001 |
| Mimikatz Credential Dump on Windows | 🟡 Intermediate | T1003.001 |
| Ransomware File Behavior Patterns | 🟡 Intermediate | T1486 |
| Lateral Movement via PsExec | 🔴 Advanced | T1570 |
| C2 Beacon Simulation via DNS Tunneling | 🔴 Advanced | T1071.004 |

Lab format for every simulation:
```
1. Environment setup
2. Execute the attack technique
3. What Wazuh captures (log, alert, rule ID)
4. Active Response option
5. Dashboard query to hunt for it at scale
```

---

## 📋 Compliance Labs

Wazuh can be a significant part of your compliance evidence — here's how to build that case:

| Framework | What's Covered |
|---|---|
| **PCI DSS** | File integrity, log retention, network traffic monitoring |
| **HIPAA** | Audit log coverage, healthcare system access monitoring |
| **GDPR** | Data access alerting, breach detection scenarios |
| **NIST CSF** | Mapping Wazuh to Identify / Protect / Detect / Respond / Recover |

Each lab maps specific Wazuh capabilities to individual compliance controls with example triggered alerts.

---

## ✍️ Rule Writing Template

Every rule in this repo follows this structure:

```xml
<!--
  Rule: Suspicious Cron Job by Non-Root User
  Author: AramEvin
  Date: 2025-01-01
  Purpose: Detects new cron entries added by unprivileged accounts —
           a common persistence method after initial access.
  Test:    As a non-root user run: echo '* * * * * /tmp/evil.sh' | crontab -
  MITRE:   T1053.003 — Scheduled Task/Job: Cron
-->
<group name="linux_persistence,custom_rules,">

  <rule id="100001" level="10">
    <if_group>syslog</if_group>
    <match>CRON\[</match>
    <regex>crontab for (?!root)</regex>
    <description>Non-root user modified crontab — possible persistence attempt</description>
    <mitre>
      <id>T1053.003</id>
    </mitre>
    <group>persistence,cron,</group>
  </rule>

</group>
```

---

## 📚 Official Wazuh Documentation

This repo complements but never replaces the official docs. Always refer to the source of truth:

| Topic | Official Link |
|---|---|
| 📖 Full Documentation | [documentation.wazuh.com](https://documentation.wazuh.com/current/index.html) |
| 🚀 Installation Guide | [Installing Wazuh](https://documentation.wazuh.com/current/installation-guide/index.html) |
| ⚙️ Agent Deployment | [Agent Enrollment](https://documentation.wazuh.com/current/user-manual/agent/agent-enrollment/index.html) |
| 📝 Custom Rules Guide | [Writing Custom Rules](https://documentation.wazuh.com/current/user-manual/ruleset/custom.html) |
| 🔁 Active Response | [Active Response Docs](https://documentation.wazuh.com/current/user-manual/capabilities/active-response/index.html) |
| ☁️ Cloud Security | [Cloud Security Monitoring](https://documentation.wazuh.com/current/cloud-security/index.html) |
| 🛡️ Compliance | [Regulatory Compliance](https://documentation.wazuh.com/current/compliance/index.html) |
| 🔗 Integrations | [Third-party Integrations](https://documentation.wazuh.com/current/user-manual/manager/manual-integration.html) |
| 🐛 Wazuh on GitHub | [wazuh/wazuh](https://github.com/wazuh/wazuh) |
| 💬 Community Slack | [Join the Community](https://wazuh.com/community/) |

---

## 🤝 Contributing

All levels welcome — from fixing a typo to submitting a full SOC simulation lab.

### How to Contribute

```bash
# 1. Fork and clone
git clone https://github.com/AramEvin/wazuh-community-hub.git

# 2. Create a branch
git checkout -b feat/my-detection-rule

# 3. Add your content following the folder structure above

# 4. Submit a pull request with a clear description
```

### Guidelines

- **Original content only** — do not copy-paste from official Wazuh docs
- **Test your rules** before submitting — include the test command in the comment header
- **Comment your configs** — explain what and why, not just how
- **Beginner awareness** — advanced content should include a Prerequisites section
- Follow [Wazuh Branding Guidelines](https://wazuh.com) when using brand assets

### Content We're Looking For

- Rules targeting specific CVEs or active malware families
- Cloud-native detections (Azure AD anomalies, GCP audit log patterns)
- Wazuh + Kubernetes runtime security labs
- Wazuh on edge/OT devices (Raspberry Pi, ICS environments)
- Non-English tutorials — contributions in any language are welcome

---

## 🌐 Community

This repo is part of the **Wazuh Ambassador Program** — a global initiative for open-source security education.

| Channel | Link |
|---|---|
| 💬 Community Slack | [wazuh.com/community](https://wazuh.com/community/) |
| 🐦 X / Twitter | `#Wazuh` · `#WazuhAmbassador` |
| 💼 LinkedIn | `#WazuhAmbassador` |
| 🌐 Wazuh Website | [wazuh.com](https://wazuh.com) |

---

## License

Original content in this repository is licensed under [MIT](./LICENSE).

> **Wazuh®** is a registered trademark of Wazuh, Inc.
> This is an independent community project and is not officially affiliated with or endorsed by Wazuh, Inc.
> All Wazuh brand assets are used in compliance with the [Wazuh Branding and Trademark Guidelines](https://wazuh.com).

---

<div align="center">

Made with ❤️ for the open-source security community

**wazuh.**

</div>
