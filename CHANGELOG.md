# Changelog

All notable changes to the **3Com 242x SNMP LLD Base Template** will be documented in this file.  
This project follows semantic versioning with incremental improvements.

---

## v1.0 (Initial Release)
- Created base SNMP LLD template for 3Com 242x switches.
- Added **interface discovery** using `ifIndex` and `ifDescr`.
- Basic item prototypes: description, admin status, operational status, speed.
- Simple triggers for **interface down** detection.

---

## v1.1
- Added **traffic monitoring** (incoming/outgoing octets).
- Introduced preprocessing for octets → bps conversion.
- Added tags for `direction` (in/out) and `component` (performance).

---

## v1.2
- Added **error counters**: input and output errors.
- Introduced sustained error monitoring via preprocessing (change per second).
- Extended triggers to include **error detection**.
- Improved tagging for reliability metrics.

---

## v1.3
- Added **utilization calculation** (basic formula using traffic/speed).
- Introduced macros for utilization thresholds (`{$IF_UTIL_WARN}`, `{$IF_UTIL_HIGH}`).
- Added triggers for **warning** and **critical utilization**.
- Optimized history/trends retention for traffic items.

---

## v1.4
- Enhanced utilization with **smoothed calculation** (averaging last 3 values).
- Reduced false positives from traffic spikes.
- Improved trigger logic for utilization alerts.
- Documentation updated with smoothed utilization explanation.

---

## v1.5
- Added **role detection** based on interface speed:
  - `uplink-100M`, `uplink-1G`, `uplink-10G`, `access`
- Introduced calculated item prototype `ifRole[{#IFINDEX}]`.
- Added structured tags for `role` to support dashboards and SLA filtering.
- Improved metadata tagging for discovered interfaces.

---

# Changelog

## v1.6
- Finalized **production-ready template** for Zabbix 7.4.
- Added **sustained error triggers** with configurable macro `{$IF_ERROR_RATE}`.
- Optimized **history/trends** settings to reduce DB load.
- Improved **structured tags** for dashboards and alerting.
- README fully documented with:
  - Overview
  - Macros
  - Discovery rules
  - Item prototypes
  - Trigger prototypes
  - Tags
  - Usage instructions
  - Notes
- Template validated and tested on Zabbix 7.4.

---

## v1.7
- Added **new macros** for discard and broadcast thresholds:
  - `{$IF_DISCARD_RATE}`
  - `{$BROADCAST_STORM}`
- Extended **item prototypes**:
  - VLAN per interface (`ifVlan[{#IFINDEX}]`)
  - MTU (`ifMtu[{#IFINDEX}]`)
  - Duplex mode (`ifDuplex[{#IFINDEX}]`)
  - Input/Output discards (`ifInDiscards`, `ifOutDiscards`)
  - Broadcast counters (`ifInBroadcastPkts`, `ifOutBroadcastPkts`)
  - Multicast counters (`ifInMulticastPkts`, `ifOutMulticastPkts`)
- Reworked **role detection**:
  - Split into separate calculated items for `uplink-10G`, `uplink-1G`, `uplink-100M`, and `access`
  - Each role tagged individually for dashboard filtering
- Added **new trigger prototypes** (existing v1.6 triggers remain unchanged):
  - Duplex mismatch detection
  - High discard rate detection
  - Broadcast storm detection
  - Multicast anomaly detection
- All new items and triggers assigned proper **UUIDs** for Zabbix import compatibility.
- Template fully validated and importable in **Zabbix 7.4**.

---

## v1.8
- Added **new item prototypes**:
  - CRC error counters (`ifInCrcErrors[{#IFINDEX}]`)
  - Collision counters (`ifCollisions[{#IFINDEX}]`)
  - Error/discard percentage KPI (`ifErrorDiscardPct[{#IFINDEX}]`)
- Added **new trigger prototypes**:
  - CRC error spike detection
  - Collision anomaly detection
  - Error/discard percentage threshold
- Extended **macros**:
  - `{$IF_CRC_THRESHOLD}`
  - `{$IF_COLLISION_THRESHOLD}`
  - `{$IF_ERROR_PCT_WARN}`
- Enhanced **role detection** and tagging for VLAN, duplex, and MTU.
- Introduced **calculated KPIs** for reliability monitoring.
- Added **dashboard graph integration** for utilization and error/discard trends.
- Automated **validation scripts** to ensure import compliance.
- Verified compatibility with **Zabbix 8.x**.

---

## v1.9.2 (Current)
- Added **native Zabbix 7.4 valuemap support**:
  - Introduced valuemaps section inside template block
  - Added UUID‑based valuemaps for:
    - 3Com-Port-Status
    - 3Com-Admin-Status
    - 3Com-Duplex-Mode
    - 3Com-Speed
- Updated **item prototypes**:
  - Standardized valuemap references to new valuemap names
  - Improved naming consistency across interface metadata items
- Enhanced **template metadata**:
  - Updated description block with clearer feature list and compatibility notes
  - Updated template version to 1.9.2
- Improved **LLD structure**:
  - Cleaned and aligned UUID patterns for all new item and trigger prototypes
  - Ensured full compatibility with Zabbix 7.4 import schema
- Improved **trigger consistency**:
  - Verified all new v1.9 triggers (SLA, latency, queue drops) for correct severity alignment
  - Updated trigger names for clarity and uniformity
- Enhanced **macro set**:
  - Validated new macros for SLA, latency, and queue thresholds
  - Improved macro descriptions and internal usage consistency
- Internal **code cleanup**:
  - Removed legacy valuemap references
  - Standardized naming conventions for valuemaps, items, and triggers
  - Improved YAML structure for readability and maintainability
- Template validated for **Zabbix 7.4** and **tested on Zabbix 8.x**
