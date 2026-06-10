<div align="center">

<img src="https://wazuh.com/uploads/2022/05/WAZUH.png" alt="Wazuh" width="220"/>

# wazuh_hub

**Community-driven detection rules · attack simulations · integrations · dashboards · compliance labs**

*Built by the community. For the community. Beyond the official docs.*

---

![Wazuh](https://img.shields.io/badge/Wazuh-4.14.5-3585F9?style=flat-square&logo=data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMTYiIGhlaWdodD0iMTYiIHZpZXdCb3g9IjAgMCAxNiAxNiIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj48Y2lyY2xlIGN4PSI4IiBjeT0iOCIgcj0iNCIgZmlsbD0iI0ZFREQwQiIvPjwvc3ZnPg==)
![License](https://img.shields.io/badge/license-MIT-0C62ED?style=flat-square)
![Contributions](https://img.shields.io/badge/contributions-welcome-FEDD0B?style=flat-square)
![Ambassador](https://img.shields.io/badge/Wazuh-Ambassador%20Program-363078?style=flat-square)

</div>

---

## What is wazuh_hub?

**wazuh_hub** is an open repository of original, practical Wazuh content that goes beyond what you find in the official documentation. Every resource here is independently created, lab-tested, and community-verified — from custom detection rules to full attack simulation walkthroughs.

This project is part of the **Wazuh Ambassador Program**. Nothing here is copied from official sources. Everything is built from real-world experience to solve real-world problems.

> 📖 For core platform documentation, always refer to the **[Official Wazuh Documentation](https://documentation.wazuh.com/current/index.html)** — this repo extends it, never replaces it.

---

## Environment

All content in this repository is developed and tested against:

| Component | Version |
|---|---|
| **Wazuh Manager** | 4.14.5 |
| **Wazuh Indexer** | 4.14.5 |
| **Wazuh Dashboard** | 4.14.5 |
| **Wazuh Agent** | 4.14.5 |

> Content may work on earlier 4.x versions but is not guaranteed. Check individual folder READMEs for version-specific notes.

---

## Repository Map

```
wazuh_hub/
│
├── 📁 detection-rules/       # Original custom XML rules by platform
│   ├── linux/                #   Linux endpoint and service log rules
│   ├── windows/              #   Windows Event Log and Sysmon rules
│   └── cloud/                #   AWS CloudTrail, Azure Activity, GCP Audit
│
├── 📁 simulations/           # Controlled attack labs with expected alerts
│   ├── brute-force/          #   SSH, RDP, web application brute force
│   ├── ransomware/           #   File encryption behavior emulation
│   └── lateral-movement/     #   Pass-the-hash, pivoting, discovery TTPs
│
├── 📁 integrations/          # Wazuh connected to external platforms
│   ├── slack/                #   Real-time alert forwarding to Slack
│   ├── thehive/              #   Automated case creation in TheHive
│   └── virustotal/           #   File hash enrichment via VirusTotal API
│
├── 📁 dashboards/            # Importable .ndjson visualisation panels
│   ├── threat-hunting/       #   MITRE ATT&CK TTP hunting views
│   ├── compliance/           #   Multi-framework compliance overview
│   └── fim/                  #   File Integrity Monitoring activity
│
├── 📁 compliance/            # Framework mapping labs and documentation
│   ├── pci-dss/              #   PCI DSS v4.0 technical control coverage
│   ├── hipaa/                #   HIPAA PHI access and integrity controls
│   └── gdpr/                 #   GDPR Article 32 technical safeguards
│
├── 📁 docs/                  # Setup guides and reference documentation
│   └── setup-guides/         #   Lab environment walkthroughs
│
├── 📄 CONTRIBUTING.md
└── 📄 LICENSE
```

---

## Quick Start

### Prerequisites

- Wazuh 4.14.5 stack (Manager + Indexer + Dashboard)
- At least one Wazuh Agent on a monitored endpoint
- Access to `/var/ossec/` on the Wazuh Manager

### Deploy a custom detection rule

```bash
# Copy the rule to your manager's custom rules directory
sudo cp detection-rules/linux/your-rule.xml /var/ossec/etc/rules/

# Test the rule logic without restarting
sudo /var/ossec/bin/wazuh-logtest

# Apply changes
sudo systemctl restart wazuh-manager
```

### Run a simulation lab

Each simulation folder contains a `README.md` with full step-by-step instructions, the simulation script, expected Wazuh alert output, and MITRE ATT&CK mapping. Start here:

```
simulations/brute-force/README.md
```

### Import a dashboard

1. Open Wazuh Dashboard → **Stack Management → Saved Objects**
2. Click **Import** and select any `.ndjson` file from `dashboards/`
3. Choose your index pattern when prompted

---

## Content Overview

| Folder | What's Inside | Level |
|---|---|---|
| [detection-rules/](detection-rules/) | Custom `.xml` rules for real-world threats | Beginner → Advanced |
| [simulations/](simulations/) | Attack labs with alert validation | Intermediate |
| [integrations/](integrations/) | SOAR and notification platform configs | Intermediate |
| [dashboards/](dashboards/) | Ready-to-import `.ndjson` panels | Beginner |
| [compliance/](compliance/) | Framework control mapping and evidence | Intermediate → Advanced |
| [docs/](docs/) | Lab setup and configuration guides | Beginner |

---

## MITRE ATT&CK Coverage

| Tactic | Techniques |
|---|---|
| Initial Access | T1190, T1078, T1566 |
| Execution | T1059, T1053 |
| Persistence | T1547, T1136 |
| Lateral Movement | T1550, T1021 |
| Exfiltration | T1041, T1048 |
| Impact | T1486, T1490 |

Full technique mappings are included in each module's `README.md`.

---

## Official Resources

| Resource | Link |
|---|---|
| Official Documentation | [documentation.wazuh.com](https://documentation.wazuh.com/current/index.html) |
| Wazuh GitHub | [github.com/wazuh](https://github.com/wazuh/wazuh) |
| Community Slack | [wazuh.com/community](https://wazuh.com/community/) |
| Ambassador Program | [wazuh.com](https://wazuh.com) |

---

## Contributing

Community contributions are welcome. See [`CONTRIBUTING.md`](CONTRIBUTING.md) for the full guide.

**Short version:**
1. Fork this repo
2. Branch: `git checkout -b feat/your-content-name`
3. Add content with a `README.md` in your subfolder
4. Open a Pull Request

---

## License

MIT License — see [LICENSE](LICENSE).

Wazuh® is a registered trademark of Wazuh Inc. This project is an independent community initiative and is not officially affiliated with or endorsed by Wazuh Inc.

---

<div align="center">

Made with ❤️ for the open-source security community

</div>
