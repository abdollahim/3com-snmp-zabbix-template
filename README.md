# 3Com 242x SNMP LLD Template for Zabbix

**Version:** 1.9  
**Compatibility:** Zabbix 7.4+ (tested on 8.x)  
**Author:** Majid Abdollahi  

---

## Overview

This template provides **production‑ready LLD monitoring** for 3Com 242x switches with extended reliability, KPI, and SLA‑oriented metrics.

### Key capabilities
- Fully **LLD interface discovery** (items & triggers auto‑created per interface)
- Monitors **traffic, speed, utilization, errors, discards, CRC, collisions**
- Supports **broadcast/multicast counters** with configurable thresholds
- Includes **queue drop monitoring**, **latency calculation**, and **SLA compliance KPI**
- Multi‑tier **uplink role detection** (`uplink-100M`, `uplink-1G`, `uplink-10G`, `access`)
- VLAN, MTU, duplex, and metadata collection with enhanced tagging
- **Smoothed utilization** to avoid false spike alerts
- Optimized **history/trends** for DB efficiency
- Prebuilt **dashboard‑friendly tags** for filtering and alerting
- Fully validated for **Zabbix 7.4**, tested on **Zabbix 8.x**

---

## Macros

| Macro                        | Default | Description                                           |
|------------------------------|---------|-------------------------------------------------------|
| `{$IF_UTIL_WARN}`            | 80      | Utilization warning threshold (%)                     |
| `{$IF_UTIL_HIGH}`            | 90      | Utilization critical threshold (%)                    |
| `{$IF_ERROR_RATE}`           | 10      | Sustained error threshold                             |
| `{$IF_DISCARD_RATE}`         | 10      | Discard rate threshold                                |
| `{$BROADCAST_STORM}`         | 1000    | Broadcast storm threshold                             |
| `{$IF_CRC_THRESHOLD}`        | 10      | CRC error spike threshold                             |
| `{$IF_COLLISION_THRESHOLD}`  | 5       | Collision anomaly threshold                           |
| `{$IF_ERROR_PCT_WARN}`       | 5       | Error/discard percentage warning threshold            |
| `{$IF_QUEUE_DROP_THRESHOLD}` | 10      | Queue drop anomaly threshold                          |
| `{$IF_LATENCY_WARN}`         | 50      | Latency warning threshold (ms)                        |
| `{$IF_SLA_MIN}`              | 95      | Minimum acceptable SLA (%)                            |

---

## Discovery Rule

- **Name:** Interface discovery  
- **Type:** SNMP_AGENT  
- **Key:** `if.discovery`  
- **OID:** `discovery[{#IFINDEX},ifIndex,{#IFNAME},ifDescr]`  
- **Delay:** 1h  
- **Filter:** Excludes loopback, VLAN, and null interfaces  
- **Result:** Automatically creates **item prototypes**, **trigger prototypes**, and **tags** for each discovered interface

---

## Item Prototypes (Summary)

| Item Name                       | Key                                | Type        | Description                                   |
|--------------------------------|------------------------------------|-------------|-----------------------------------------------|
| Description                    | `ifAlias[{#IFINDEX}]`              | SNMP        | Interface description                          |
| Oper/Admin status              | `ifOperStatus`, `ifAdminStatus`    | SNMP        | Up/Down state                                  |
| Speed                          | `ifSpeed[{#IFINDEX}]`              | SNMP        | Interface speed                                |
| Traffic (in/out)               | `ifInOctets`, `ifOutOctets`        | SNMP        | bps with preprocessing                         |
| Utilization (smoothed)         | `if.util.smoothed`                 | CALCULATED  | Smoothed utilization (%)                       |
| Errors (in/out)                | `ifInErrors`, `ifOutErrors`        | SNMP        | Errors per second                              |
| Discards (in/out)              | `ifInDiscards`, `ifOutDiscards`    | SNMP        | Discards per second                            |
| CRC errors                     | `ifInCrcErrors`                    | SNMP        | CRC error counter                              |
| Collisions                     | `ifCollisions`                     | SNMP        | Collision counter                              |
| Broadcast/Multicast counters   | `ifInBroadcastPkts`, `ifOutBroadcastPkts`, `ifInMulticastPkts`, `ifOutMulticastPkts` | SNMP | L2 anomaly detection |
| Queue drops                    | `ifQueueDrops`                     | SNMP        | Queue drop counter                             |
| Latency                        | `ifLatency`                        | CALCULATED  | Basic latency estimation                       |
| SLA compliance                 | `ifSLA`                            | CALCULATED  | SLA % based on errors/discards                 |
| Error/discard % KPI            | `ifErrorDiscardPct`                | CALCULATED  | Reliability KPI                                |
| Role                           | `ifRole`                           | CALCULATED  | Uplink/access classification                    |
| VLAN / MTU / Duplex            | `ifVlan`, `ifMtu`, `ifDuplex`      | SNMP        | Interface metadata                             |

---

## Trigger Prototypes (Summary)

| Trigger Name                                | Description                                   | Priority |
|---------------------------------------------|-----------------------------------------------|----------|
| Interface DOWN                              | Admin up but oper down                        | AVERAGE  |
| Utilization > warn/high                     | Smoothed utilization                           | WARNING / HIGH |
| Sustained error rate                        | Based on `{$IF_ERROR_RATE}`                   | WARNING  |
| High discard rate                           | Based on `{$IF_DISCARD_RATE}`                 | WARNING  |
| Duplex mismatch                             | Wrong duplex at high speed                    | WARNING  |
| Broadcast storm                             | Based on `{$BROADCAST_STORM}`                 | HIGH     |
| Multicast anomaly                           | Sudden multicast spike                        | WARNING  |
| CRC error spike                             | Based on `{$IF_CRC_THRESHOLD}`                | WARNING  |
| Collision anomaly                           | Based on `{$IF_COLLISION_THRESHOLD}`          | INFO     |
| Error/discard % > threshold                 | KPI‑based reliability alert                   | WARNING  |
| Queue drop anomaly                          | Based on `{$IF_QUEUE_DROP_THRESHOLD}`         | WARNING  |
| Latency > threshold                         | Based on `{$IF_LATENCY_WARN}`                 | WARNING  |
| SLA < minimum                               | Based on `{$IF_SLA_MIN}`                      | HIGH     |

---

## Tags

- **interface:** `{#IFNAME}`
- **direction:** in / out
- **component:** metadata / status / performance / reliability / kpi
- **role:** uplink‑100M / uplink‑1G / uplink‑10G / access

---

## Features Summary

- Fully **LLD** (no manual per‑port configuration)
- Multi‑tier **uplink detection**
- Broadcast, multicast, CRC, collision, discard, queue drop monitoring
- KPI‑based metrics: **error/discard %, SLA, latency**
- Dashboard‑friendly tagging
- Optimized history/trends for performance
- Zabbix **7.4+ compatible**, tested on **8.x**

---

## Usage Instructions

1. Import the latest template YAML (`3Com_242x_SNMP_LLD_Base_v1.9.yaml`) into Zabbix.
2. Assign the template to any 3Com 242x switch host.
3. Adjust macros as needed for your environment.
4. Interfaces will be discovered automatically.
5. Use tags for dashboards, filtering, and alert routing.

---

## Notes

- Template is fully LLD and scales to any number of ports.
- Role detection automatically assigns uplink/access categories.
- Smoothed utilization reduces false alerts.
- KPI‑based triggers provide deeper reliability insights.
- All items and triggers are auto‑generated per interface.

---

**End of README**
