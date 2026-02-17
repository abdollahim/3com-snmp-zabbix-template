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

## v1.6 (Current)
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

