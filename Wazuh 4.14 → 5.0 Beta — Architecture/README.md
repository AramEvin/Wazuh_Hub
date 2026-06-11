# Wazuh 4.14 → 5.0 Beta — Architecture Deep Dive

<p align="center">
  <img src="https://img.shields.io/badge/Wazuh_4.14-Current_Stable-22c55e?style=for-the-badge&labelColor=000000"/>
  <img src="https://img.shields.io/badge/Wazuh_5.0-Beta_April_2026-FEDD0B?style=for-the-badge&labelColor=000000"/>
  <img src="https://img.shields.io/badge/Breaking_Points-15-ef4444?style=for-the-badge&labelColor=000000"/>
  <img src="https://img.shields.io/badge/Migration-Blue--Green_Required-3585F9?style=for-the-badge&labelColor=000000"/>
</p>

> ⚠️ **Beta Disclaimer** — This document is based on **Wazuh 5.0 Beta 1 (April 2026)**.
> Binaries, configuration schemas, file paths, and API contracts may change before General Availability.
> Always validate against [documentation.wazuh.com](https://documentation.wazuh.com) before applying changes to production.

---

## Table of Contents

- [The Pipeline Is Completely Rebuilt](#1-the-pipeline-is-completely-rebuilt)
- [What Is Actually New in 5.0](#2-what-is-actually-new-in-50)
- [Path Change: /var/ossec → /var/wazuh-manager](#3-path-change-varossec--varwazuh-manager)
- [Components Removed in 5.0](#4-components-removed-in-50)
- [The 15 Breaking Points](#5-the-15-breaking-points)
- [Migration Strategy](#6-migration-strategy)
- [Windows Log Collection: Field Name Changes](#7-windows-log-collection-field-name-changes)
- [Current Status](#8-current-status)

---

## 1. The Pipeline Is Completely Rebuilt

The entire event ingestion path has been redesigned. Filebeat — the source of the most common large-scale production failure in 4.x — is gone.

| Wazuh 4.14 (legacy) | Wazuh 5.0 Beta (new) |
|---|---|
| Agent → analysisd (C) → alerts.json → Filebeat → Indexer | Agent → remoted → Engine (C++) → indexer-connector → Indexer |
| Filebeat: separate OS process, no backpressure signal | indexer-connector: in-process, real-time backpressure loop |
| Disk exhaustion risk when Indexer saturates | EPS throttled at source when Indexer under pressure |
| analysisd: C, multithreaded, per-decoder queues | Engine: C++ rewrite, parallel policy workers via Orchestrator |

> In 4.x, when the Indexer saturated above ~50k EPS, Filebeat spooled to disk with no signal back to analysisd — causing silent disk exhaustion on the manager. In 5.0, the `indexer-connector` lives inside the Engine and raises a real-time backpressure flag, throttling intake at the source before disk pressure builds.

---

## 2. What Is Actually New in 5.0

### C++ Engine (replaces analysisd)

- Complete rewrite from C to C++ — parallel policy workers via Orchestrator
- In-process IOC matching and GeoIP enrichment — no external process spawning
- EPS throttle at input level, not per-decoder queue
- Live metrics endpoint: current EPS, queue depth, connector node states

### eBPF File Integrity Monitoring

- No more auditd dependency for who-data on Linux
- Precompiled eBPF driver ships with the agent — no compilation on endpoint
- Auto-falls back to auditd if kernel version is below 5.8

| Dimension | 4.14 — auditd | 5.0 — eBPF |
|---|---|---|
| Kernel requirement | Any | ≥ 5.8 (fallback to auditd) |
| Event latency | 100–500 ms | < 10 ms |
| Transfer method | File tail (audit.log) | Zero-copy ring_buffer |
| auditd dependency | Required | None |
| auditd contention | Yes (shared netlink) | No |
| eUID capture | Complex parse | Native from hook |
| PPID capture | Limited | Native |
| Log rotation gap | Yes | No |

### Native Cyber Threat Intelligence

- `wazuh-integratord` removed — no more per-alert API calls exhausting VirusTotal quota
- IOC matching is in-stream and in-process: microseconds vs seconds
- CVE data: Wazuh CTI platform → Indexer → Engine (Indexer is now authoritative)
- Air-gapped environments: use offline CVE bundle via `offline-url` config option

### Cluster by Default

- Every 5.0 server runs as a cluster node from first boot — `cluster.disabled` is removed
- Agent group state moves from filesystem to `wazuh-groups` Indexer index
- RBAC sourced from Indexer — no more inter-node file sync for security config
- CMSync: each Engine instance pulls decoder/ruleset from Indexer at startup

---

## 3. Path Change: /var/ossec → /var/wazuh-manager

The manager installation root has moved. **Agent-side paths are completely unchanged.**

| Resource | 4.x path | 5.0 path |
|---|---|---|
| Main config | `/var/ossec/etc/ossec.conf` | `/var/wazuh-manager/etc/wazuh-server.yml` |
| Rules | `/var/ossec/etc/rules/` | `/var/wazuh-manager/etc/rules/` |
| Decoders | `/var/ossec/etc/decoders/` | `/var/wazuh-manager/etc/decoders/` |
| Logs | `/var/ossec/logs/` | `/var/wazuh-manager/logs/` |
| Binaries | `/var/ossec/bin/` | `/var/wazuh-manager/bin/` |
| Keystore | `/var/ossec/bin/wazuh-keystore` | `/var/wazuh-manager/bin/wazuh-keystore` |
| Alerts JSON | `/var/ossec/logs/alerts/alerts.json` | `/var/wazuh-manager/logs/alerts/alerts.json` |
| Agent keys | `/var/ossec/etc/client.keys` | `/var/wazuh-manager/etc/client.keys` |

> ⚠️ A compatibility symlink `/var/ossec → /var/wazuh-manager` is created at install. **Do NOT rely on it permanently** — it may be removed in a future 5.x patch. Audit all cron jobs, Ansible playbooks, Nagios checks, Jenkins pipelines, and logrotate configs immediately.

---

## 4. Components Removed in 5.0

| Component removed | Replaced by / reason |
|---|---|
| **Filebeat** | `indexer-connector` (in-process inside Engine) |
| **wazuh-integratord** | Native CTI engine (in-stream IOC matching) |
| **wazuh-agentlessd** | Deprecated capability — removed |
| **wazuh-maild** | Removed — use external alerting integrations |
| **wazuh-dbd** | Removed — database output connector |
| **ossec-authd (C binary)** | `wazuh-authd` (Python) or REST API enrollment |
| **manage_agents CLI** | REST API `/agents` endpoints |
| **agent-auth CLI** | REST API or `wazuh-authd` |
| **Agent ID 000** | Full agent/manager path separation achieved |
| **NSIS Windows installer** | MSI only from 5.0 onwards |
| **OpenSCAP server module** | Removed entirely |
| **cluster.disabled option** | Cluster is always enabled — option removed from schema |

---

## 5. The 15 Breaking Points

> Miss any single one of these and your migration will fail — some loudly, some silently.
> **Validate all 15 before cutting over.**

| # | Breaking point | Symptom | Fix |
|---|---|---|---|
| 1 | 4.x agents cannot connect to 5.0 manager | Agents show `NEVER_CONNECTED` | Upgrade ALL agents via WPK before LB flip |
| 2 | `/var/ossec/` path references in automation | Scripts fail silently (ENOENT) | Audit and update all cron, Ansible, Nagios |
| 3 | `filebeat.service` absent after upgrade | Monitoring alerts fire | Remove Filebeat from systemd, CI, monitoring |
| 4 | `cluster.disabled` tag still in `ossec.conf` | Manager fails to start | Remove the `<disabled>` tag from cluster config |
| 5 | `initial_master_nodes` in Indexer config | Indexer cluster fails to bootstrap | Rename to `initial_cluster_manager_nodes` |
| 6 | Removed daemons still in systemd/monitoring | Startup scripts and alerts fail | Remove `agentlessd`, `maild`, `dbd` from targets |
| 7 | `wazuh-integratord` scripts still configured | VirusTotal/Slack/PD fail silently | Migrate to native CTI or webhook integrations |
| 8 | Windows NSIS `.exe` installer used | GPO/SCCM/Intune deployments fail | Update all tooling to `.msi` package |
| 9 | Legacy OS agents (RHEL5, Solaris, AIX, HP-UX) | No 5.0 WPK available — upgrade fails | Freeze at 4.x or plan decommission |
| 10 | `manage_agents` / `agent-auth` CLI in scripts | Scripts calling binaries fail | Migrate to REST API equivalents |
| 11 | Old `wazuh-keystore` path in rotation scripts | Credential rotation scripts fail | Update to `/var/wazuh-manager/bin/wazuh-keystore` |
| 12 | Rootcheck server-side DB queries | Custom reporting scripts fail | Query findings via Indexer alert pipeline |
| 13 | `ossec-authd` (C binary) called directly | Scripts invoking it fail | Use `wazuh-authd` or REST API enrollment |
| 14 | Custom rules using 4.x field names | Rules stop firing silently | Remap to WCS fields; test with `wazuh-logtest` |
| 15 | `node.roles: [master]` in Indexer IaC | Puppet/Ansible templates fail | Replace `master` with `cluster_manager` |

---

## 6. Migration Strategy

> Beta 1 is explicit: upgrading from 4.x requires a full stack restart and approximately **10 minutes of downtime** for the Indexer data migration step. In-place upgrade is not recommended for production. Use a blue-green approach.

### Recommended 4-Phase Blue-Green Approach

**Phase 1 — Build 5.0 stack in parallel**
Deploy new Indexer cluster and manager nodes alongside the existing 4.x stack. Zero impact on production.

**Phase 2 — Upgrade ALL agents via WPK**
Must be complete before Phase 3. The agent wire protocol was rewritten — 4.x agents cannot connect to a 5.0 manager.

**Phase 3 — Flip the load balancer**
Switch `:1514` and `:1515` backends to 5.0 manager nodes. This is your ~5-minute maintenance window.

**Phase 4 — Validate and clean up**
Confirm pipeline, remove Filebeat, update all automation paths.

### Pre-Cutover Validation

```bash
/var/wazuh-manager/bin/wazuh-logtest -q
```

Paste identical test cases as your pre-migration baseline. Rule IDs must match, WCS field names must match, decoder output must match.

---

## 7. Windows Log Collection: Field Name Changes

In 4.x, the agent parsed Windows Event XML into key-value pairs before forwarding. In 5.0, raw `EvtRender()` XML is forwarded and decoded by the engine using the **Wazuh Common Schema (WCS)**. Every custom Windows rule must be updated.

| 4.x field name | 5.0 WCS field name | Type change |
|---|---|---|
| `win.system.eventID` | `winlog.event_id` | → Integer |
| `win.system.channel` | `winlog.channel` | — |
| `win.system.computer` | `winlog.computer_name` | — |
| `win.eventdata.ipAddress` | `source.ip` / `winlog.event_data.*` | WCS |
| `win.eventdata.targetUserName` | `user.name` | WCS |
| `win.eventdata.targetDomainName` | `user.domain` | WCS |
| `win.eventdata.logonType` | `winlog.event_data.LogonType` | — |
| `win.system.severityValue` | `log.level` | WCS |
| `win.system.keywords` | `winlog.keywords` | → Array |
| `data.srcip` | `source.ip` | WCS |
| `data.dstip` | `destination.ip` | WCS |
| `data.srcuser` | `user.name` | WCS |

---

## 8. Current Status

| Version | Status | Notes |
|---|---|---|
| **Wazuh 4.14** | ✅ Current stable | Production-ready |
| **Wazuh 5.0 Beta 1** | 🟡 Beta — April 2026 | ~10 min downtime for Indexer migration step |
| **Wazuh 5.0 GA** | ⏳ Pending | Track at [github.com/wazuh/wazuh](https://github.com/wazuh/wazuh) |

---

## Resources

| Resource | Link |
|---|---|
| Wazuh Documentation | [documentation.wazuh.com](https://documentation.wazuh.com) |
| Wazuh on GitHub | [github.com/wazuh/wazuh](https://github.com/wazuh/wazuh) |
| Wazuh CTI Platform | [cti.wazuh.com](https://cti.wazuh.com) |
| Wazuh Community | [community.wazuh.com](https://community.wazuh.com) |

---

<p align="center">
  <b>Wazuh 5.0 Beta — Architecture Reference</b><br/>
  Maintained by <a href="https://github.com/AramEvin">AramEvin</a> — Wazuh Ambassador<br/>
  <i>Community document — not officially affiliated with Wazuh, Inc.</i>
</p>
