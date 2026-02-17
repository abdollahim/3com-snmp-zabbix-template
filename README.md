# 3Com 242x SNMP LLD Template for Zabbix

**Version:** 1.6  
**Compatibility:** Zabbix 7.4+  
**Author:** Majid Abdollahi  

---

## Overview

This template provides **production-ready LLD monitoring** for 3Com 242x switches:

- Fully **LLD interface discovery** (items & triggers auto-created per interface)
- Monitors **traffic, speed, utilization, input/output errors, admin/oper status**
- Supports **multi-tier uplink detection** (`uplink-100M`, `uplink-1G`, `uplink-10G`, `access`)
- Includes **per-interface macros** for easy threshold tuning
- Uses **structured tags** for filtering, dashboards, and alert actions
- Optional VLAN monitoring per interface
- **Smoothed utilization** to avoid false spike alerts
- Optimized **history/trends** settings to reduce DB load

---

## Macros

| Macro             | Default | Description                               |
|------------------|--------|-------------------------------------------|
| `{$IF_UTIL_WARN}` | 80     | Utilization warning threshold (%)         |
| `{$IF_UTIL_HIGH}` | 90     | Utilization critical threshold (%)        |
| `{$IF_ERROR_RATE}`| 10     | Error rate threshold for sustained errors |

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

## Item Prototypes

| Item Name                       | Key                          | Type          | Description                               | Tags                                 | History/Trends |
|--------------------------------|------------------------------|--------------|-------------------------------------------|-------------------------------------|----------------|
| Interface {#IFNAME}: description| ifAlias[{#IFINDEX}]          | SNMP_AGENT   | Interface description (from SNMP ifAlias)| interface, component:metadata        | 30d / 0        |
| Interface {#IFNAME}: operational status | ifOperStatus[{#IFINDEX}] | SNMP_AGENT | Up/Down status | interface, component:status | 15d / 0 |
| Interface {#IFNAME}: admin status | ifAdminStatus[{#IFINDEX}]  | SNMP_AGENT   | Admin Up/Down status                       | interface, component:status        | 15d / 0        |
| Interface {#IFNAME}: speed      | ifSpeed[{#IFINDEX}]          | SNMP_AGENT   | Interface speed (bps)                      | interface, component:performance   | 15d / 90d      |
| Interface {#IFNAME}: incoming traffic | ifInOctets[{#IFINDEX}] | SNMP_AGENT | Incoming traffic in bps                     | direction:in, component:performance| 15d / 90d      |
| Interface {#IFNAME}: outgoing traffic | ifOutOctets[{#IFINDEX}] | SNMP_AGENT | Outgoing traffic in bps                     | direction:out, component:performance| 15d / 90d      |
| Interface {#IFNAME}: utilization smoothed | if.util.smoothed[{#IFINDEX}] | CALCULATED | Interface utilization (%) smoothed          | interface, component:performance    | 15d / 90d      |
| Interface {#IFNAME}: input errors | ifInErrors[{#IFINDEX}]     | SNMP_AGENT   | Input errors / sec                          | interface, direction:in, component:reliability | 15d / 90d |
| Interface {#IFNAME}: output errors | ifOutErrors[{#IFINDEX}]   | SNMP_AGENT   | Output errors / sec                         | interface, direction:out, component:reliability | 15d / 90d |
| Interface {#IFNAME}: role       | ifRole[{#IFINDEX}]           | CALCULATED   | Port role based on speed (uplink-100M / uplink-1G / uplink-10G / access) | interface, role, component:metadata | 15d / 0 |

---

## Trigger Prototypes

| Trigger Name                               | Expression                                                                                  | Priority | Description                                |
|-------------------------------------------|--------------------------------------------------------------------------------------------|----------|--------------------------------------------|
| Interface {#IFNAME} is DOWN               | `last(/3Com 242x SNMP LLD Base/ifOperStatus[{#IFINDEX}])<>1 and last(/3Com 242x SNMP LLD Base/ifAdminStatus[{#IFINDEX}])=1` | AVERAGE  | Triggers when interface is down but admin up |
| Interface {#IFNAME} utilization >{$IF_UTIL_WARN} | `last(/3Com 242x SNMP LLD Base/if.util.smoothed[{#IFINDEX}])>{$IF_UTIL_WARN}`                       | WARNING  | Trigger for utilization warning            |
| Interface {#IFNAME} utilization >{$IF_UTIL_HIGH} | `last(/3Com 242x SNMP LLD Base/if.util.smoothed[{#IFINDEX}])>{$IF_UTIL_HIGH}`                      | HIGH     | Trigger for utilization critical           |
| Interface {#IFNAME} sustained error rate  | `(change(/3Com 242x SNMP LLD Base/ifInErrors[{#IFINDEX}],#2)>{$IF_ERROR_RATE}) or (change(/3Com 242x SNMP LLD Base/ifOutErrors[{#IFINDEX}],#2)>{$IF_ERROR_RATE})` | WARNING  | Trigger for sustained errors on interface |

---

## Tags

- **interface:** {#IFNAME}  
- **direction:** in/out (for traffic/errors)  
- **component:** metadata / status / performance / reliability  
- **role:** uplink-100M / uplink-1G / uplink-10G / access  

---

## Features Summary

- Fully **LLD**: no per-port duplication  
- Supports **multi-tier uplink detection**  
- Includes **sustained error triggers**  
- Smoothed utilization to avoid false spikes  
- Uses **per-interface macros** for flexible thresholds  
- Optimized **history/trends** settings  
- Zabbix **7.4-compatible YAML**  
- Production-ready for **3Com 242x switches**  

---

## Usage Instructions

1. Import the `3Com_242x_SNMP_LLD_Base_v1.6.yaml` template into Zabbix 7.4.
2. Assign the template to any 3Com 242x switch hosts.
3. Adjust macros if needed (`{$IF_UTIL_WARN}`, `{$IF_UTIL_HIGH}`, `{$IF_ERROR_RATE}`).
4. Monitor discovered interfaces automatically.
5. Use tags to filter dashboards or set alert actions.

---

## Notes

- Template is **fully LLD**, scalable to any number of ports.
- Role detection automatically assigns **uplink-100M, uplink-1G, uplink-10G, or access**.
- Smoothed utilization calculation reduces false alerts on traffic spikes.
- All triggers and items are automatically generated per interface.

---

**End of README**
