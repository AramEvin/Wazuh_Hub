# wazuh_hub 🛡️

> **A community-driven knowledge base for Wazuh practitioners** — original detection rules, attack simulations, integrations, dashboards, and compliance labs built by the community, for the community.

<p align="center">
  <img src="https://wazuh.com/uploads/2022/05/WAZUH.png" alt="Wazuh" height="60"/>
</p>

<p align="center">
  <a href="https://github.com/YOUR_USERNAME/wazuh_hub/stargazers"><img src="https://img.shields.io/github/stars/YOUR_USERNAME/wazuh_hub?style=flat-square&color=3585F9" alt="Stars"/></a>
  <a href="https://github.com/YOUR_USERNAME/wazuh_hub/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-3585F9?style=flat-square" alt="License"/></a>
  <a href="https://wazuh.com"><img src="https://img.shields.io/badge/built%20for-Wazuh%204.x-0C62ED?style=flat-square" alt="Wazuh"/></a>
  <a href="CONTRIBUTING.md"><img src="https://img.shields.io/badge/contributions-welcome-FEDD0B?style=flat-square" alt="Contributing"/></a>
</p>

---

## What is this?

**wazuh_hub** is an open community repository focused on original, practical Wazuh content that goes beyond the official documentation. Every resource here is independently created to help security practitioners learn, experiment, and level up their Wazuh deployments — from home labs to production SOCs.

This is part of the **Wazuh Ambassador Program** initiative. All content is original, tested, and community-verified.

---

## Repository Map

```
wazuh_hub/
│
├── 📁 detection-rules/       # Custom XML detection rules by OS and platform
│   ├── linux/                #   Rules for Linux endpoints and services
│   ├── windows/              #   Rules for Windows events and Sysmon
│   └── cloud/                #   Rules for AWS, Azure, GCP log sources
│
├── 📁 simulations/           # Step-by-step attack simulation labs
│   ├── brute-force/          #   SSH/RDP/web brute force scenarios
│   ├── ransomware/           #   Ransomware behavior emulation
│   └── lateral-movement/     #   Pass-the-hash, pivoting, discovery
│
├── 📁 integrations/          # Connecting Wazuh to external platforms
│   ├── slack/                #   Alert forwarding to Slack channels
│   ├── thehive/              #   Automated case creation in TheHive
│   └── virustotal/           #   IOC enrichment via VirusTotal API
│
├── 📁 dashboards/            # Importable Wazuh Dashboard visualizations
│   ├── threat-hunting/       #   TTP hunting panels
│   ├── compliance/           #   Compliance overview dashboards
│   └── fim/                  #   File Integrity Monitoring views
│
├── 📁 compliance/            # Compliance mapping labs and documentation
│   ├── pci-dss/              #   PCI DSS v4 coverage with Wazuh
│   ├── hipaa/                #   HIPAA technical safeguard mapping
│   └── gdpr/                 #   GDPR Article 32 technical controls
│
├── 📁 docs/                  # Guides, architecture diagrams, references
│   ├── setup-guides/         #   Lab setup walkthroughs
│   └── architecture/         #   Wazuh deployment diagrams
│
├── 📄 CONTRIBUTING.md        # How to contribute to this repo
└── 📄 LICENSE                # MIT License
```

---

## Quick Start

### Prerequisites

- Wazuh 4.x (Server + Indexer + Dashboard) — see [`docs/setup-guides/`](docs/setup-guides/)
- Linux or Windows endpoints with Wazuh Agent installed
- Basic familiarity with Wazuh rules and alerts

### Using custom detection rules

```bash
# Copy a rule file to your Wazuh manager
sudo cp detection-rules/linux/local_rules.xml /var/ossec/etc/rules/

# Validate and reload
sudo /var/ossec/bin/wazuh-logtest
sudo systemctl restart wazuh-manager
```

### Running a simulation lab

Each simulation folder contains a `README.md` with full step-by-step instructions, expected alert triggers, and MITRE ATT&CK mapping. Start with:

```
simulations/brute-force/README.md
```

---

## Content Categories

| Category | What's Inside | Skill Level |
|---|---|---|
| [Detection Rules](detection-rules/) | Custom `.xml` rules for real-world threats | Beginner → Advanced |
| [Simulations](simulations/) | Controlled attack labs with expected detections | Intermediate |
| [Integrations](integrations/) | Scripts and configs for SOAR/notification platforms | Intermediate |
| [Dashboards](dashboards/) | Importable `.ndjson` visualisation panels | Beginner |
| [Compliance](compliance/) | Framework mapping and evidence documentation | Intermediate → Advanced |
| [Docs](docs/) | Setup walkthroughs and architecture notes | Beginner |

---

## MITRE ATT&CK Coverage

Content in this repo is mapped to MITRE ATT&CK wherever applicable. Each detection rule and simulation includes ATT&CK tactic and technique references in its README.

| Tactic | Techniques Covered |
|---|---|
| Initial Access | T1190, T1078, T1566 |
| Execution | T1059, T1053 |
| Persistence | T1547, T1136 |
| Lateral Movement | T1550, T1021 |
| Exfiltration | T1041, T1048 |
| Impact | T1486, T1490 |

> Full mapping details are included in each module's `README.md`.

---

## How to Contribute

Community contributions are welcome and encouraged. See [`CONTRIBUTING.md`](CONTRIBUTING.md) for full guidelines.

**Short version:**
1. Fork this repository
2. Create a feature branch: `git checkout -b feat/your-rule-name`
3. Add your content following the folder conventions
4. Include a `README.md` in your subfolder
5. Open a Pull Request with a clear description

Every rule, simulation, or guide submitted is reviewed for accuracy and originality before merging.

---

## Community & Support

| Channel | Link |
|---|---|
| Wazuh Community Slack | [wazuh.com/community](https://wazuh.com/community/) |
| Wazuh GitHub | [github.com/wazuh](https://github.com/wazuh) |
| Official Documentation | [documentation.wazuh.com](https://documentation.wazuh.com) |
| Ambassador Program | [wazuh.com/ambassador](https://wazuh.com) |

---

## License

This repository is released under the [MIT License](LICENSE). All content is original and independently created by contributors. Wazuh® is a registered trademark of Wazuh Inc. This project is not officially affiliated with or endorsed by Wazuh Inc.

---

<p align="center">Made with ❤️ for the open-source security community</p>
