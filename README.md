<div align="center">

<img src="https://img.shields.io/badge/Wazuh-SIEM%20Documentation%20Hub-0066CC?style=for-the-badge&logo=shield&logoColor=white" alt="Wazuh Hub"/>

# 🛡️ Wazuh_Hub

**A comprehensive open-source knowledge base for deploying, tuning, and mastering Wazuh SIEM**

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)
[![Wazuh](https://img.shields.io/badge/Wazuh-4.x-blue?style=flat-square&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PC9zdmc+)](https://wazuh.com)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](CONTRIBUTING.md)
[![Documentation](https://img.shields.io/badge/docs-in%20progress-orange?style=flat-square)](#)
[![Stars](https://img.shields.io/github/stars/YOUR_USERNAME/Wazuh_Hub?style=flat-square&color=yellow)](https://github.com/YOUR_USERNAME/Wazuh_Hub/stargazers)

<br/>

> *From fresh install to production-grade threat detection — everything in one place.*

</div>

---

## 📖 What is Wazuh_Hub?

**Wazuh_Hub** is a structured documentation repository covering all aspects of [Wazuh](https://wazuh.com) — the open-source SIEM, XDR, and compliance platform. Whether you are setting up your first agent or engineering advanced detection pipelines, this hub has you covered.

```
📦 Wazuh_Hub
 ┣ 📂 installation/        → Step-by-step setup guides
 ┣ 📂 detection-rules/     → Custom rules & alert tuning
 ┣ 📂 integrations/        → Third-party integration tutorials
 ┗ 📂 architecture/        → Diagrams & deployment notes
```

---

## 🗂️ Table of Contents

- [🚀 Installation & Setup](#-installation--setup)
- [🔍 Detection Rules & Alerts](#-detection-rules--alerts)
- [🔗 Integration Tutorials](#-integration-tutorials)
- [🏗️ Architecture & Diagrams](#-architecture--diagrams)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)

---

## 🚀 Installation & Setup

> Step-by-step guides for deploying Wazuh in various environments.

| Guide | Description | Status |
|---|---|---|
| [Single-Node Install](installation/single-node.md) | All-in-one Wazuh stack on one machine | ✅ Done |
| [Multi-Node Cluster](installation/multi-node.md) | Distributed setup for production | ✅ Done |
| [Docker Deployment](installation/docker.md) | Containerized stack with Docker Compose | ✅ Done |
| [Agent Enrollment](installation/agent-enrollment.md) | Linux, Windows & macOS agent setup | 🔄 In Progress |
| [Kubernetes Deployment](installation/kubernetes.md) | Helm chart & K8s manifests | 📝 Planned |

---

## 🔍 Detection Rules & Alerts

> Custom Wazuh rules, decoders, and alert fine-tuning documentation.

| Topic | Description | Status |
|---|---|---|
| [Custom Rules Basics](detection-rules/custom-rules-basics.md) | Writing your first detection rule | ✅ Done |
| [Decoder Development](detection-rules/decoders.md) | Parsing custom log formats | ✅ Done |
| [Brute Force Detection](detection-rules/brute-force.md) | SSH, RDP, and web login attacks | ✅ Done |
| [Privilege Escalation](detection-rules/privilege-escalation.md) | Linux & Windows escalation alerts | 🔄 In Progress |
| [Tuning False Positives](detection-rules/false-positives.md) | Reducing noise in your alerts | 📝 Planned |

---

## 🔗 Integration Tutorials

> Connecting Wazuh with the tools in your security ecosystem.

| Integration | Description | Status |
|---|---|---|
| [TheHive](integrations/thehive.md) | Case management & incident response | ✅ Done |
| [Shuffle SOAR](integrations/shuffle.md) | Automated playbooks with Shuffle | ✅ Done |
| [Slack Alerts](integrations/slack.md) | Real-time notifications to Slack | ✅ Done |
| [MISP Threat Intel](integrations/misp.md) | IOC enrichment with MISP | 🔄 In Progress |
| [Grafana Dashboard](integrations/grafana.md) | Metrics visualization with Grafana | 📝 Planned |
| [Cortex Analyzers](integrations/cortex.md) | Automated artifact analysis | 📝 Planned |

---

## 🏗️ Architecture & Diagrams

> Visual references for understanding and designing Wazuh deployments.

| Diagram | Description | Status |
|---|---|---|
| [Single-Node Architecture](architecture/single-node-diagram.md) | Overview of a standalone deployment | ✅ Done |
| [Multi-Node Architecture](architecture/multi-node-diagram.md) | Indexer cluster + manager nodes | ✅ Done |
| [Agent ↔ Manager Flow](architecture/agent-manager-flow.md) | Log and alert data flow diagram | 🔄 In Progress |
| [Network Segmentation](architecture/network-segmentation.md) | Secure placement within a network | 📝 Planned |

---

## 🤝 Contributing

Contributions are warmly welcome! If you have a guide, a custom rule, or a diagram to add:

1. **Fork** this repository
2. **Create** a branch: `git checkout -b feature/your-topic`
3. **Commit** your changes: `git commit -m "Add: your topic description"`
4. **Push** to your branch: `git push origin feature/your-topic`
5. **Open** a Pull Request

Please follow the [contribution guidelines](CONTRIBUTING.md) and keep documentation clear and concise.

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<div align="center">

Made with ❤️ for the open-source security community

⭐ **If this hub helped you, consider giving it a star!** ⭐

</div>
