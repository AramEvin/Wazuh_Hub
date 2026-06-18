# Wazuh 4.x — High Availability & Deployment Decision Guide

> Real-World Infrastructure Decisions · HA Architecture · Networking · OS Requirements  
> Wazuh Ambassador Content Hub | Original Practitioner Knowledge | 2025  
> Version 4.x — Not copied from documentation

---

## 1. Why High Availability Matters

Most Wazuh deployments start as a quick all-in-one install. When the disk fills up and the indexer crashes at 3am, you learn why HA matters the hard way. **Every recommendation in this guide is something that broke at least once in production without it.**

> A single Wazuh node is a single point of failure for your entire security visibility. When the Manager is down — no alerts. When the Indexer is full — no storage. When the Dashboard crashes — no visibility.

### What Fails First in Production

| Failure Point | Time to First Failure | Impact | Would HA Have Prevented It? |
|---|---|---|---|
| Wazuh Indexer disk full | 2–6 weeks | Alerts stop being stored — silent blind spot | Yes — ILM + multi-node |
| Wazuh Manager OOM | Variable (spikes under attack) | Rules stop processing, agents disconnect | Yes — worker nodes |
| Wazuh Dashboard unresponsive | Random (OpenSearch query load) | Analysts lose visibility | Partial — load balancer |
| Agent cert expiry | 1 year post-install | Mass agent disconnection | No — process issue |
| Single NIC failure | Hardware-dependent | All connectivity lost | Yes — bonded NICs |
| OS kernel panic | Hardware-dependent | Full node down | Yes — cluster failover |

---

## 2. Deployment Types

There are **eight ways to deploy Wazuh**. The right choice depends on: number of agents, your environment, and whether you need to survive component failures.

### 2.1 All-in-One (Single Node)

Everything on one machine — Manager, Indexer, Dashboard. Fast to set up. Fast to break under load.

**Use this when:**
- Home lab or personal learning environment
- Ambassador content creation / proof-of-concept demos
- Monitoring fewer than 30 endpoints with low log volume

**Do NOT use this when:**
- Anything running after next month
- Any environment with compliance requirements (PCI, HIPAA, ISO 27001)
- Agents generating more than 500 events/second aggregate

**OS Requirements:**

| Item | Minimum (Lab) | Recommended (Lab) |
|---|---|---|
| OS | Ubuntu 20.04 LTS | Ubuntu 22.04 LTS |
| CPU | 4 cores | 8 cores |
| RAM | 8 GB | 16 GB |
| Disk | 50 GB SSD | 200 GB NVMe |
| Swap | 4 GB | 0 GB (disabled) |

> **Practitioner Note:** Disable swap immediately after OS install on any Wazuh node. Run `sudo swapoff -a` and remove the swap entry from `/etc/fstab`. This single step prevents a large category of mysterious slowdowns and crashes.

---

### 2.2 Standard Multi-Node Production Deployment

Each component lives on dedicated infrastructure. The Indexer runs as a 3-node cluster. This is where **HA actually begins**.

**Node Architecture:**

| Node Role | Count | Purpose | Can Share Hardware? |
|---|---|---|---|
| Wazuh Indexer (Master-eligible) | 3 | Stores alerts, handles queries, votes on cluster state | No — dedicated only |
| Wazuh Indexer (Data-only) | 0–N | Add for read/write throughput beyond 3 master nodes | No — dedicated only |
| Wazuh Manager (Master) | 1 | Rule processing, active response, agent auth | No — dedicated only |
| Wazuh Manager (Worker) | 1–N | Additional rule processing capacity | No — dedicated only |
| Wazuh Dashboard | 1–2 | Web UI — can be behind a load balancer | Yes — with Manager in a pinch |
| Load Balancer (agents) | 1–2 | Distribute agent connections across Manager nodes | Yes |

> **Why 3 Indexer Nodes?** OpenSearch uses quorum-based election. With 2 nodes: lose 1, lose quorum, cluster stops. With 3 nodes: lose 1, you still have 2 of 3 — quorum holds. Always run 3+ master-eligible nodes; never 2.  
> **Split-Brain Warning:** Never run 2 master-eligible Indexer nodes. Set `minimum_master_nodes` to `(n/2)+1`.

**Production OS Requirements per Node:**

| Node | OS | CPU | RAM | Disk | Network |
|---|---|---|---|---|---|
| Indexer (each) | Ubuntu 22.04 / RHEL 9 | 8–16 cores | 32 GB | 1 TB NVMe SSD | 10 Gbps |
| Manager Master | Ubuntu 22.04 / RHEL 9 | 8 cores | 16 GB | 200 GB SSD | 10 Gbps |
| Manager Worker | Ubuntu 22.04 / RHEL 9 | 8 cores | 16 GB | 200 GB SSD | 10 Gbps |
| Dashboard | Ubuntu 22.04 / RHEL 9 | 4 cores | 8 GB | 100 GB SSD | 1 Gbps |
| Load Balancer | Any Linux | 2 cores | 4 GB | 50 GB | 10 Gbps |

**Port Reference:**

| Connection | Port | Protocol | Direction | Notes |
|---|---|---|---|---|
| Agent → Manager | 1514 | TCP/UDP | Inbound to Manager | TCP preferred — UDP loses packets under load |
| Agent → Manager (enrollment) | 1515 | TCP | Inbound to Manager | One-time enrollment only |
| Manager → Indexer | 9200 | TCP | Outbound from Mgr | NEVER expose to internet |
| Indexer cluster (internal) | 9300 | TCP | Bidirectional | Must be open between ALL indexer nodes |
| Dashboard → Indexer | 9200 | TCP | Outbound from Dash | — |
| Analyst → Dashboard | 443 | TCP | Inbound to Dash | Always HTTPS |
| Manager cluster (internal) | 1516 | TCP | Bidirectional | Only needed with worker nodes |
| Syslog intake (optional) | 514 | UDP | Inbound to Manager | For devices that cannot run an agent |

---

### 2.3 Virtual Machine / OVA Deployment

Pre-packaged all-in-one virtual appliance. Fastest path to a running Wazuh, but with trade-offs.

**Advantages:** Fastest setup (~5 min), hypervisor snapshots for rollback, VM isolation, easy to clone for training labs, hypervisor live migration (vMotion/KVM migrate) for host-level HA.

**Disadvantages:** OVA is always the most recent release at publication — no version choice; storage expansion requires two separate operations; nested VMs (OVA inside a cloud VM) are significantly slower.

**Hypervisor Requirements:**

| Hypervisor | Min Version | vCPU | RAM | Network Mode |
|---|---|---|---|---|
| VirtualBox | 6.1+ | 4 | 8 GB | Bridged — NAT breaks agent connectivity |
| VMware Workstation | 16+ | 4 | 8 GB | Bridged recommended |
| VMware ESXi | 7.0+ | 4 | 8 GB | Port group with VLAN |
| KVM/libvirt | Any modern | 4 | 8 GB | Bridge mode |
| Hyper-V | 2019+ | 4 | 8 GB | External virtual switch only |

---

### 2.4 Docker Deployment

Ideal for engineers who prefer container workflows. The compose file handles complexity, but its abstractions also hide problems.

**Advantages:** Full stack up in under 3 minutes, version pinning, easy upgrade testing, container resource limits, portable configuration.

**Disadvantages:** Named volumes can be accidentally deleted with `docker compose down -v`; `vm.max_map_count=262144` must be set on the HOST; memory limits must be coordinated between JVM heap and container limit; single Docker host is still one failure point.

**Host OS Requirements:**

| Requirement | Value | Why |
|---|---|---|
| `vm.max_map_count` | 262144 (permanent in `/etc/sysctl.conf`) | Indexer refuses to start without this |
| Docker Engine | 24.0+ (Docker CE, not Docker Desktop) | Wazuh needs compose v2 syntax |
| Host RAM | 16 GB minimum | 8 GB container limit leaves nothing for the OS |
| Host Disk | 200 GB+ | Default volume location at `/var/lib/docker` fills the root partition |
| Kernel | 5.4+ | Older kernels have cgroup v1 issues with OpenSearch |
| Swap on host | Disabled | Container memory limits are meaningless if host swap is enabled |

**Critical `docker-compose.yml` settings for the Indexer container:**
```yaml
services:
  wazuh.indexer:
    mem_limit: 4g
    environment:
      - bootstrap.memory_lock=true
      - "OPENSEARCH_JAVA_OPTS=-Xms2g -Xmx2g"
    ulimits:
      memlock:
        soft: -1
        hard: -1
      nofile:
        soft: 65536
        hard: 65536
    volumes:
      - wazuh-indexer-data:/var/lib/wazuh-indexer  # Named volume only — never bind mount
```

---

### 2.5 Kubernetes Deployment

The correct answer for enterprise-scale Wazuh where HA is non-negotiable. The gap between "running" and "production-ready" is significant.

**Advantages:** Automatic pod restart, StatefulSets for stable Indexer identity, Horizontal Pod Autoscaler, rolling zero-downtime upgrades, multi-zone deployment for true infrastructure HA.

**Disadvantages:** Complex to configure correctly; persistent storage is cloud-provider-specific; requires anti-affinity rules (see below); agent connectivity requires a stable external IP; Kubernetes cluster itself must be maintained.

**Infrastructure Requirements:**

| Component | Pods | CPU Request | Memory Request | Storage |
|---|---|---|---|---|
| Wazuh Indexer | 3 (StatefulSet) | 4 cores | 8 GB | 500 GB gp3 SSD per pod |
| Wazuh Manager | 1 master | 2 cores | 4 GB | 100 GB per pod |
| Manager Worker | 1–N | 2 cores | 4 GB | 100 GB per pod |
| Wazuh Dashboard | 1–2 | 1 core | 2 GB | 50 GB |

**Critical — Anti-Affinity Rules for Real HA:**

Without pod anti-affinity, Kubernetes can schedule all three Indexer pods on the same physical node. This is the **most common Kubernetes Wazuh mistake**.

```yaml
affinity:
  podAntiAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
    - labelSelector:
        matchExpressions:
        - key: app
          operator: In
          values:
          - wazuh-indexer
      topologyKey: kubernetes.io/hostname  # One pod per NODE maximum
```

**Kubernetes Service Types:**

| Service | Type | Port | Notes |
|---|---|---|---|
| Agent enrollment | LoadBalancer | 1515 | Stable external IP — put in DNS |
| Agent events | LoadBalancer | 1514 | Must not change after deploy |
| Dashboard access | Ingress / LB | 443 | Use Nginx Ingress with cert-manager |
| Indexer REST API | ClusterIP | 9200 | Internal only — never external |
| Indexer transport | ClusterIP | 9300 | Handled by headless service |

---

### 2.6 Offline / Air-Gapped Deployment

Same capabilities as Standard Installation, but every update must cross the air gap manually.

**Use when:** Classified environments, FedRAMP, IL4/IL5, HIPAA strict, or other frameworks requiring no outbound connectivity from production security infrastructure.

**The Update Problem:**

| Data Type | Update Frequency Needed | Impact if Stale |
|---|---|---|
| Vulnerability DB (CVE data) | Weekly minimum | New CVEs not detected |
| Wazuh ruleset updates | Monthly | New attack patterns not covered |
| MITRE ATT&CK mappings | Quarterly | Technique coverage gaps |
| TLS certificates | Annual | Agents disconnect — mass blind spot |

---

### 2.7 Ansible Deployment

Not a deployment type — a deployment **method**. Produces the same result as a Standard Installation but automatically and reproducibly across multiple nodes.

**Advantages:** Deploy a full cluster with one command, idempotent runs, version-controlled infrastructure, disaster recovery via playbook re-run.

**Requirements:**

| Requirement | Details |
|---|---|
| Ansible version | 2.12+ on control node |
| Python on targets | 3.8+ on all managed nodes |
| SSH | Key-based auth, no password prompt |
| Privilege escalation | `sudo` without password for ansible user |
| Inventory | Accurate hostnames/IPs — wrong inventory = wrong role on wrong node |

---

## 3. High Availability Architecture — Decision Matrix

### 3.1 HA Tiers

| HA Tier | Deployment | RTO | RPO (Data Loss) | Cost Multiplier | Best For |
|---|---|---|---|---|---|
| Tier 0 — None | All-in-One / OVA | > 1 hour | Up to 24 hours | 1x | Labs and learning only |
| Tier 1 — Basic | Standard + daily backup | 30–60 min | Up to 24 hours | 1.5x | Small org, < 100 agents |
| Tier 2 — Component HA | 3-node Indexer cluster | 0–5 min | < 1 minute | 3x | Mid-size org, 100–500 agents |
| Tier 3 — Full HA | 3-Indexer + 2-Manager | 0–30 seconds | Near zero | 5x | Large org, 500–2000 agents |
| Tier 4 — Enterprise | Kubernetes multi-zone | Seconds | Zero | 8–10x | Enterprise / MSSP, 2000+ agents |

### 3.2 Tier 2 — Component HA Architecture

The deployment most mid-size organizations should target. Three Indexer nodes give fault tolerance for one node failure.

| Node | OS | vCPU | RAM | Disk | NIC Speed | Role |
|---|---|---|---|---|---|---|
| wazuh-indexer-01 | Ubuntu 22.04 LTS | 8 | 32 GB | 1 TB NVMe | 10 Gbps | Indexer — master eligible |
| wazuh-indexer-02 | Ubuntu 22.04 LTS | 8 | 32 GB | 1 TB NVMe | 10 Gbps | Indexer — master eligible |
| wazuh-indexer-03 | Ubuntu 22.04 LTS | 8 | 32 GB | 1 TB NVMe | 10 Gbps | Indexer — master eligible |
| wazuh-manager-01 | Ubuntu 22.04 LTS | 8 | 16 GB | 200 GB SSD | 10 Gbps | Manager master |
| wazuh-dashboard-01 | Ubuntu 22.04 LTS | 4 | 8 GB | 100 GB SSD | 1 Gbps | Dashboard + Nginx reverse proxy |

**Critical Indexer Tuning:**
- JVM Heap: set to 16 GB (50% of 32 GB RAM). Never exceed 31 GB.
- Sharding: `number_of_shards=3`, `number_of_replicas=1` — lose one node, no data loss.
- ILM: rollover at 50 GB per index or 30 days (whichever comes first), delete phase at 90 days.

### 3.3 Tier 3 — Full HA Architecture

Adds a Manager worker node so agent load is distributed and the cluster survives a Manager failure with brief reconnection rather than full outage. Minimum for 500+ agents.

**Changes from Tier 2:**
- Manager worker node added (same spec as master)
- Load balancer deployed in front of Manager nodes (HAProxy or cloud NLB)
- Agents connect to load balancer VIP on port 1514
- Port 1516 must be open between Manager nodes
- `cluster.conf` must be configured on both Manager nodes

**HAProxy Configuration for Agent Load Balancing:**
```
# /etc/haproxy/haproxy.cfg

frontend wazuh_agents
    bind *:1514
    default_backend wazuh_managers

backend wazuh_managers
    balance leastconn
    option tcp-check
    server manager-01 10.0.1.10:1514 check inter 10s rise 2 fall 3
    server manager-02 10.0.1.11:1514 check inter 10s rise 2 fall 3

# Enrollment — only master handles enrollment
frontend wazuh_enrollment
    bind *:1515
    default_backend wazuh_master

backend wazuh_master
    server manager-01 10.0.1.10:1515 check
```

---

## 4. Production vs Testing

Testing and production environments are **not** the same with different hardware. They have different security requirements, failure modes, and operational needs.

| Dimension | Testing / Lab | Production |
|---|---|---|
| TLS certificates | Self-signed (auto-generated) | Signed by internal or trusted CA |
| Default passwords | Acceptable temporarily | Must be changed before first agent enrolled |
| Index retention | 7–14 days | 90–365 days (compliance driven) |
| Backup | Nice to have | Required — daily, tested monthly |
| Firewall rules | Permissive | Strict — only required ports open |
| NTP | Host clock usually fine | All nodes must use same NTP source |
| Swap | Acceptable | Must be disabled |
| Agent enrollment | Manual is fine | Automated with pre-shared key or API |
| Monitoring | Manual spot checks | External monitoring required |

---

## 5. Universal Best Practices

### 5.1 Five Things You Must Do Before Going Live

**1. Disable swap on every Wazuh node**
```bash
sudo swapoff -a
# Then comment out the swap line in /etc/fstab
```

**2. Set `vm.max_map_count` permanently**
```bash
echo 'vm.max_map_count=262144' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```
Without this, the Indexer will fail to start — on bare metal, VMs, and Docker hosts alike.

**3. Configure Index Lifecycle Management before the first alert**
Set rollover at `max_size=50gb` or `max_age=30d`. Without ILM, the index grows until disk is full and alert storage silently stops.

**4. Change all default passwords before enrolling the first agent**
```bash
/usr/share/wazuh-indexer/plugins/opensearch-security/tools/wazuh-passwords-tool.sh
```

**5. Synchronize NTP across ALL nodes before installation**
Time drift > 30 seconds causes TLS handshake failures and false alert timestamps.
```bash
timedatectl set-ntp true   # Ubuntu
chronyc tracking            # Verify sync
```

### 5.2 Alert Volume and Disk Planning

The #1 operational failure in Wazuh is disk full on the Indexer.

| Agent Type | Avg Events/Day | Compressed Storage/Day | 30-Day Storage | 90-Day Storage |
|---|---|---|---|---|
| Windows workstation | 5,000–15,000 | 5–15 MB | 150–450 MB | 450 MB–1.3 GB |
| Windows server | 20,000–80,000 | 20–80 MB | 600 MB–2.4 GB | 1.8–7.2 GB |
| Linux server (standard) | 10,000–30,000 | 10–30 MB | 300–900 MB | 900 MB–2.7 GB |
| Linux server (web-facing) | 50,000–200,000 | 50–200 MB | 1.5–6 GB | 4.5–18 GB |
| Network device (syslog) | 30,000–100,000 | 30–100 MB | 900 MB–3 GB | 2.7–9 GB |

**Storage formula:**
```
(agents) × (avg MB/day) × (retention days) × 1.3 overhead multiplier = required disk
```

### 5.3 Security Hardening Checklist

- [ ] Port 9200 (Indexer REST API) not reachable outside the Wazuh network segment
- [ ] Port 9300 (Indexer transport) only reachable between Indexer nodes
- [ ] Dashboard port 443 terminated at Nginx or cloud load balancer — not self-signed
- [ ] Agent enrollment port 1515 blocked after initial enrollment is complete
- [ ] Dedicated OS users for each Wazuh service — do not run as root in production
- [ ] Wazuh API authentication enabled
- [ ] `ossec.conf` active-response settings reviewed before production
- [ ] Wazuh dashboard users audited quarterly

### 5.4 Minimum Backup Configuration

Backup everything in `/var/ossec/etc/` — this is your entire Wazuh configuration.

```bash
#!/bin/bash
BACKUP_DIR="/opt/wazuh-backups/$(date +%Y-%m-%d)"
mkdir -p $BACKUP_DIR

# Config and rules
cp -r /var/ossec/etc $BACKUP_DIR/

# Wazuh Indexer index snapshot
curl -X PUT "https://localhost:9200/_snapshot/wazuh_backup/$(date +%Y%m%d)" \
  -H 'Content-Type: application/json' -u admin:$INDEXER_PASSWORD \
  -d '{"indices":"wazuh-alerts-*","ignore_unavailable":true}'

# Rotate backups older than 30 days
find /opt/wazuh-backups -maxdepth 1 -type d -mtime +30 -exec rm -rf {} +
```

---

## 6. Quick Decision Card

| Your Situation | Agents | Right Deployment | HA Tier | First Thing to Do |
|---|---|---|---|---|
| Learning Wazuh personally | 1–5 | All-in-One | Tier 0 | Snapshot the VM before anything |
| Ambassador content / demos | 1–10 | All-in-One or OVA | Tier 0 | Note the version used in your content |
| Small business, budget-constrained | 10–50 | Standard single-node | Tier 1 | Set up daily backup to external storage |
| Mid-size org, need reliability | 50–300 | 3-node Indexer cluster | Tier 2 | Configure ILM before enrolling first agent |
| Large org, 24/7 SOC | 300–1000 | Full HA — 3 Indexer + 2 Manager | Tier 3 | Deploy HAProxy for agent load balancing |
| MSSP / multi-tenant | > 1000 | Kubernetes multi-zone | Tier 4 | Get Kubernetes storage sorted first |
| Air-gapped / classified | Any | Offline Standard Install | Tier 2+ | Build the update transfer process before production |
| DevOps team, IaC-first | Any | Ansible + Standard Install | Tier 2+ | Test playbook on staging before production run |

---

*Wazuh Ambassador Content Hub | Original practitioner knowledge | 2025*  
