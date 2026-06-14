<div align="center">

<img src="https://wazuh.com/uploads/2022/05/WAZUH.png" alt="Wazuh" width="180"/>

# wazuh_hub

**Original community content for Wazuh practitioners**

Detection rules · Attack simulations · Integrations · Dashboards · Compliance labs

---

![Wazuh](https://img.shields.io/badge/Wazuh-4.14.5-0C62ED?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-3585F9?style=flat-square)
![Ambassador](https://img.shields.io/badge/Wazuh-Ambassador%20Program-FEDD0B?style=flat-square&labelColor=363078)
![Contributions](https://img.shields.io/badge/Contributions-Welcome-brightgreen?style=flat-square)

</div>

---

## About

**wazuh_hub** is an independent community repository created under the **Wazuh Ambassador Program**. Every piece of content here is original — written and tested from real-world experience, not reproduced from official sources.

The goal is to go beyond what official documentation covers: practical labs, custom detection logic, and real attack scenarios that help Wazuh practitioners learn by doing.

> 📖 **New to Wazuh?** Start with the [Official Documentation](https://documentation.wazuh.com/current/index.html) first — this repository extends it, not replaces it.

---

## Environment

All content is developed and tested on:

| Component | Version |
|---|---|
| Wazuh Manager | **4.14.5** |
| Wazuh Indexer | **4.14.5** |
| Wazuh Dashboard | **4.14.5** |
| Wazuh Agent | **4.14.5** |

---

## What You Will Find Here

Each folder contains original, ready-to-use content with its own README explaining the scenario, prerequisites, and expected results.

| Folder | Description |
|---|---|
| [`detection-rules/`](detection-rules/) | Custom XML rules for Linux, Windows, and cloud platforms |
| [`simulations/`](simulations/) | Step-by-step attack labs with expected Wazuh alert output |
| [`integrations/`](integrations/) | Connecting Wazuh to Slack, TheHive, VirusTotal, and more |
| [`dashboards/`](dashboards/) | Importable `.ndjson` panels for threat hunting and compliance |
| [`compliance/`](compliance/) | PCI DSS, HIPAA, and GDPR coverage demonstrations |
| [`docs/`](docs/) | Setup guides for building Wazuh lab environments |

---

## License

MIT — see [LICENSE](LICENSE).

*Wazuh® is a registered trademark of Wazuh Inc. This is an independent community project, not officially affiliated with Wazuh Inc.*
