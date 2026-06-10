<div align="center">

<br/>

# wazuh-hub

**Wazuh Ambassador Program — Original Community Content**

<br/>

[![Ambassador](https://img.shields.io/badge/Wazuh-Ambassador_Program-3585F9?style=for-the-badge)](https://wazuh.com/ambassador)
[![License](https://img.shields.io/badge/License-MIT-2DA4AA?style=for-the-badge)](./LICENSE)
[![Topics](https://img.shields.io/badge/Topics-8_Planned-0C62ED?style=for-the-badge)]()
[![Content](https://img.shields.io/badge/Content-Original_%26_Lab--Tested-000000?style=for-the-badge)]()

<br/>

> *Free · Open · Lab-validated · Written for everyone*

<br/>

</div>

---

## What Is This?

**wazuh-hub** is an open community knowledge base built as part of the **Wazuh Ambassador Program**.

Every guide, detection rule, script, and walkthrough here was created from scratch — not copied from official documentation. The goal is to fill the gaps: real labs, honest explanations, practical rules, and content that goes beyond the official docs.

Whether you are setting up your first Wazuh instance, running a production SOC, or planning a major version upgrade — there is something here for you.

---

## How to Use This Repository

- **Read on GitHub** — every README renders cleanly in your browser, no setup needed
- **Clone and run** — scripts and rules work directly in your lab
- **Follow the links** — every document links to related topics
- **Check back often** — new topics are added regularly

**Not sure where to start?**

| I want to... | Go here |
|---|---|
| Understand what Wazuh is | [01-all-about-wazuh](./01-all-about-wazuh/) |
| Run a hands-on attack lab | [02-soc-simulation-lab](./02-soc-simulation-lab/) |
| Plan a 4.x → 5.0 upgrade | [03-wazuh-5-migration](./03-wazuh-5-migration/) |
| Deploy detection rules now | [02-soc-simulation-lab/rules](./02-soc-simulation-lab/rules/) |

---

## Repository Structure

```
wazuh-hub/
│
├── README.md                        ← You are here
├── LICENSE
│
├── 01-all-about-wazuh/              ← What Wazuh is and how it works
│   ├── README.md                    ← Topic index
│   ├── overview/README.md           ← The big picture
│   ├── architecture/README.md       ← How all the pieces connect
│   ├── components/README.md         ← Agent · Manager · Indexer · Dashboard
│   └── use-cases/README.md          ← What people actually use it for
│
├── 02-soc-simulation-lab/           ← Full red-vs-blue attack & detection lab
│   ├── README.md                    ← Complete lab walkthrough
│   ├── rules/
│   │   └── lab_custom_rules.xml     ← 13 detection rules, deploy-ready
│   └── scripts/
│       ├── setup-agent.sh           ← Automated agent configuration
│       └── cleanup.sh               ← Full lab cleanup
│
├── 03-wazuh-5-migration/            ← 4.14 → 5.0 architecture & migration guide
│   └── README.md
│
├── 04-home-lab-setup/               ← Coming soon
├── 05-integrations/                 ← Coming soon
├── 06-cloud-security/               ← Planned
├── 07-compliance-labs/              ← Planned
└── 08-threat-hunting/               ← Planned
```

---

## All Topics

| # | Topic | Level | Status | What's Inside |
|---|-------|-------|--------|---------------|
| [01](./01-all-about-wazuh/) | All About Wazuh | 🟢 Beginner | ✅ Ready | Overview · Architecture · Components · Use Cases |
| [02](./02-soc-simulation-lab/) | SOC Simulation Lab | 🟡 Intermediate | ✅ Ready | 9 attack phases · 13 rules · 100% detection · scripts |
| [03](./03-wazuh-5-migration/) | Wazuh 5.0 Migration | 🔴 Advanced | ✅ Ready | 15 breaking points · eBPF FIM · blue-green strategy |
| [04](./04-home-lab-setup/) | Home Lab Setup | 🟢 Beginner | 🔄 Coming Soon | VirtualBox · single node · agent enrollment |
| [05](./05-integrations/) | Integrations | 🟡 Intermediate | 🔄 Coming Soon | Slack · TheHive · VirusTotal · MISP |
| [06](./06-cloud-security/) | Cloud Security | 🟡 Intermediate | 📋 Planned | AWS · Azure · GCP |
| [07](./07-compliance-labs/) | Compliance Labs | 🔴 Advanced | 📋 Planned | PCI DSS · HIPAA · GDPR · NIST |
| [08](./08-threat-hunting/) | Threat Hunting | 🔴 Advanced | 📋 Planned | TTPs · MITRE ATT&CK · Dashboard queries |

---

## Published Content

### [01 — All About Wazuh](./01-all-about-wazuh/)
**Level:** Beginner → Intermediate

Four guides that answer every foundational question about Wazuh before you touch a terminal. What it is, how the architecture works, what each component does, and how real organizations use it.

---

### [02 — SOC Simulation Lab](./02-soc-simulation-lab/)
**Level:** Intermediate

A complete red-vs-blue lab. Kali Linux attacks a Wazuh-monitored Ubuntu endpoint across 9 phases. Every technique detected. All 13 custom rules fired. Detection latency under 30 seconds. Full cleanup scripts included.

---

### [03 — Wazuh 5.0 Migration Guide](./03-wazuh-5-migration/)
**Level:** Advanced

The most significant architecture change in Wazuh history. Filebeat removed, C++ engine rewrite, eBPF FIM, 15 breaking points that will silently or loudly break your deployment, and a proven blue-green migration strategy.

---

## Quick Clone

```bash
git clone https://github.com/YOUR_USERNAME/wazuh-hub.git
cd wazuh-hub
```

No setup required to read. Clone if you want to run the scripts or deploy the detection rules.

---

## Contributing

Contributions are welcome. One rule: **content must be original.**

```bash
git checkout -b topic/your-topic-name
# Add a new numbered folder with its own README.md
git commit -m "Add: your topic description"
# Open a pull request
```

---

## About

Built as part of the **[Wazuh Ambassador Program](https://wazuh.com/ambassador)** — a community initiative to grow open-source security knowledge worldwide.

- Community: [community.wazuh.com](https://community.wazuh.com)
- Official docs: [documentation.wazuh.com](https://documentation.wazuh.com)
- Issues and ideas: [Open an issue](../../issues)

---

<div align="center">
<br/>
<sub>MIT License · Independent community content · Not affiliated with Wazuh, Inc.</sub><br/>
<sub>Wazuh® is a registered trademark of Wazuh, Inc.</sub>
<br/><br/>
</div>
