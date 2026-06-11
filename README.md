<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Wazuh_Logo.svg/320px-Wazuh_Logo.svg.png" alt="Wazuh" width="280"/>
</p>

<h1 align="center">wazuh-community-hub</h1>

<p align="center">
  <b>The open-source community knowledge base for Wazuh defenders.</b><br/>
  Custom detection rules · Integration guides · SOC simulations · Compliance labs · Tutorials
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
</p>

---

> **This is not a mirror of official Wazuh documentation.**
> Every file here is original, community-built content — detection logic, integration recipes, attack simulations, and walkthroughs created from real-world experience.
> Official docs live at [documentation.wazuh.com](https://documentation.wazuh.com/current/index.html).

---

## 📋 Table of Contents

- [What Is This?](#-what-is-this)
- [Repository Structure](#️-repository-structure)
- [Quick Navigation](#-quick-navigation)
- [Detection Rules](#-detection-rules)
- [Integrations](#-integrations)
- [SOC Simulations](#-soc-simulations)
- [Compliance Labs](#-compliance-labs)
- [Rule Writing Template](#-rule-writing-template)
- [Official Wazuh Resources](#-official-wazuh-resources)
- [Contributing](#-contributing)
- [Community](#-community)
- [License](#-license)

---

## 🛡️ What Is This?

**wazuh-community-hub** is a living knowledge base built by and for the Wazuh community.

Whether you're a SOC analyst, a home lab builder, a security student, or an enterprise engineer — this repo gives you something you can use immediately: rules you can deploy, labs you can run, and integrations you can wire up today.

| Audience | What to look for |
|---|---|
| 🆕 Just getting started | [`/tutorials/beginner`](./tutorials/beginner/) — setup guides, first alerts, basic rules |
| ⚙️ Running Wazuh already | [`/detection-rules`](./detection-rules/) + [`/integrations`](./integrations/) |
| 🔴 Blue team / SOC | [`/soc-simulations`](./soc-simulations/) — attack/detect labs with MITRE mapping |
| 📋 Compliance focus | [`/compliance-labs`](./compliance-labs/) — PCI, HIPAA, GDPR, NIST |

---

## 🗂️ Repository Structure

```
wazuh-community-hub/
│
├── 📁 detection-rules/          # Original custom Wazuh XML rules
│   ├── linux/                   # Linux endpoint detection
│   ├── windows/                 # Windows endpoint detection
│   ├── cloud/                   # AWS, Azure, GCP rules
│   └── README.md
│
├── 📁 integrations/             # Step-by-step integration guides
│   ├── slack/                   # Alert routing to Slack channels
│   ├── thehive/                 # Auto case creation in TheHive
│   ├── shuffle/                 # SOAR playbook triggers
│   ├── virustotal/              # FIM hash enrichment
│   ├── misp/                    # Threat intel feed correlation
│   └── README.md
│
├── 📁 soc-simulations/          # Attack scenarios + expected Wazuh output
│   ├── brute-force/
│   ├── ransomware-behavior/
│   ├── lateral-movement/
│   └── README.md
│
├── 📁 compliance-labs/          # Wazuh mapped to compliance controls
│   ├── pci-dss/
│   ├── hipaa/
│   ├── gdpr/
│   ├── nist/
│   └── README.md
│
├── 📁 tutorials/                # Original walkthroughs by skill level
│   ├── beginner/
│   ├── intermediate/
│   ├── advanced/
│   └── README.md
│
├── 📁 dashboards/               # Custom dashboard JSON exports
├── 📁 scripts/                  # Deployment and automation helpers
└── 📁 resources/                # Cheat sheets, diagrams, reference cards
```

---

## ⚡ Quick Navigation

| I want to… | Go here |
|---|---|
| 🆕 Install and configure Wazuh | [`/tutorials/beginner`](./tutorials/beginner/) |
| 🧠 Deploy custom detection rules | [`/detection-rules`](./detection-rules/) |
| 🔗 Connect Wazuh to Slack / TheHive / MISP | [`/integrations`](./integrations/) |
| 🧪 Simulate attacks and validate alerts | [`/soc-simulations`](./soc-simulations/) |
| 📋 Build compliance evidence with Wazuh | [`/compliance-labs`](./compliance-labs/) |
| 📊 Import custom dashboards | [`/dashboards`](./dashboards/) |
| 🛠️ Automate agent deployment | [`/scripts`](./scripts/) |

---

## 🔴 Detection Rules

Original XML rules covering threat scenarios not addressed in default Wazuh rulesets.

| Rule File | What It Detects | Platform | MITRE ATT&CK |
|---|---|---|---|
| `linux_cron_persistence.xml` | Non-root cron job modifications — persistence indicator | Linux | [T1053.003](https://attack.mitre.org/techniques/T1053/003/) |
| `win_encoded_powershell.xml` | Base64-encoded PowerShell execution chains | Windows | [T1059.001](https://attack.mitre.org/techniques/T1059/001/) |
| `aws_s3_public_exposure.xml` | S3 bucket ACL changes granting public access | AWS | [T1530](https://attack.mitre.org/techniques/T1530/) |
| `docker_escape_attempt.xml` | Container escape via privileged process execution | Docker | [T1611](https://attack.mitre.org/techniques/T1611/) |
| `ssh_tunnel_detection.xml` | SSH port forwarding and tunneling behavior | Linux | [T1572](https://attack.mitre.org/techniques/T1572/) |
| `win_lsass_access.xml` | LSASS memory access — credential theft indicator | Windows | [T1003.001](https://attack.mitre.org/techniques/T1003/001/) |
| `cloud_iam_privilege_escalation.xml` | IAM role/policy changes enabling privilege escalation | AWS/Azure | [T1078.004](https://attack.mitre.org/techniques/T1078/004/) |

**Rule ID ranges used in this repo:**

| Range | Category |
|---|---|
| `100001 – 100999` | Linux endpoint rules |
| `101000 – 101999` | Windows endpoint rules |
| `102000 – 102999` | Cloud (AWS / Azure / GCP) |
| `103000 – 103999` | Container / Kubernetes |
| `104000 – 104999` | Application / web server |

> Every rule ships with a comment header, a lab test command, and a recommended active response pairing.

---

## 🔗 Integrations

Working, tested configs for extending Wazuh into your security ecosystem:

| Integration | What It Does | Guide |
|---|---|---|
| **Slack** | Severity-based alert routing with custom message format | [`/integrations/slack`](./integrations/slack/) |
| **TheHive** | Auto case creation from critical/high Wazuh alerts | [`/integrations/thehive`](./integrations/thehive/) |
| **Shuffle** | SOAR playbook triggers mapped to Wazuh rule groups | [`/integrations/shuffle`](./integrations/shuffle/) |
| **VirusTotal** | FIM hash enrichment on new or changed files | [`/integrations/virustotal`](./integrations/virustotal/) |
| **MISP** | Threat intel correlation with live Wazuh events | [`/integrations/misp`](./integrations/misp/) |

Each guide includes: prerequisites → config file → how to verify it's working → troubleshooting.

---

## 🧪 SOC Simulations

Hands-on labs pairing real attack techniques with the Wazuh detections they trigger:

| Simulation | Difficulty | MITRE Technique | Active Response? |
|---|---|---|---|
| SSH Brute Force → Detection + Block | 🟢 Beginner | [T1110.001](https://attack.mitre.org/techniques/T1110/001/) | ✅ Yes |
| Mimikatz Credential Dump | 🟡 Intermediate | [T1003.001](https://attack.mitre.org/techniques/T1003/001/) | ✅ Yes |
| Ransomware File Behavior Simulation | 🟡 Intermediate | [T1486](https://attack.mitre.org/techniques/T1486/) | ✅ Yes |
| Lateral Movement via PsExec | 🔴 Advanced | [T1570](https://attack.mitre.org/techniques/T1570/) | ⚙️ Manual |
| C2 Beacon via DNS Tunneling | 🔴 Advanced | [T1071.004](https://attack.mitre.org/techniques/T1071/004/) | ⚙️ Manual |

**Every lab follows this format:**

```
1. Environment setup
2. Execute the attack technique  
3. What Wazuh captures (log source, rule ID, alert level)
4. Active response option (if available)
5. Dashboard query for hunting at scale
```

---

## 📋 Compliance Labs

Build evidence-backed compliance posture using Wazuh:

| Framework | Coverage Area |
|---|---|
| **PCI DSS** | File integrity monitoring, log retention, network segmentation alerts |
| **HIPAA** | Audit log completeness, PHI system access monitoring |
| **GDPR** | Data access alerting, breach detection scenarios, retention compliance |
| **NIST CSF** | Mapping Wazuh to Identify / Protect / Detect / Respond / Recover |

Each lab maps individual Wazuh capabilities to specific compliance controls with triggered alert examples.

---

## ✍️ Rule Writing Template

All rules in this repo follow this standard structure:

```xml
<!--
  Rule    : Suspicious Cron Job by Non-Root User
  Author  : AramEvin
  Date    : 2025-01-01
  Purpose : Detects cron entries added by unprivileged accounts —
            a reliable indicator of persistence after initial access.
  Test    : As a non-root user run:
              echo '* * * * * /tmp/payload.sh' | crontab -
  MITRE   : T1053.003 — Scheduled Task/Job: Cron
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

## 📚 Official Wazuh Resources

This repo complements but never replaces official documentation. Always verify against the source of truth:

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
| 🐛 Wazuh on GitHub | [wazuh/wazuh](https://github.com/wazuh/wazuh) |
| 💬 Community Slack | [Join Here](https://wazuh.com/community/) |
| 🐦 Follow on X | [@wazuh](https://twitter.com/wazuh) |

---

## 🤝 Contributing

All levels welcome — from a typo fix to a full simulation lab.

### Steps

```bash
# 1. Fork and clone
git clone https://github.com/AramEvin/wazuh-community-hub.git

# 2. Create a feature branch
git checkout -b feat/your-contribution-name

# 3. Add content following the folder structure
# 4. Open a pull request with a clear description
```

### Guidelines

- **Original content only** — do not copy from official Wazuh documentation
- **Test rules before submitting** — include the test command in the comment header
- **Comment every config** — explain what it does and why, not just how
- **Include prerequisites** for intermediate/advanced content
- Respect [Wazuh Brand Guidelines](https://wazuh.com) when using any brand assets

### Ideas We're Looking For

- Rules targeting specific CVEs or active malware families
- Azure AD / GCP audit log detection scenarios
- Wazuh + Kubernetes runtime threat detection
- Non-English tutorials (Arabic, Spanish, French, Turkish, etc.)
- Wazuh on edge/OT/ICS environments

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
  <strong>wazuh-community-hub</strong> · Maintained by <a href="https://github.com/AramEvin">AramEvin</a>
</p>
