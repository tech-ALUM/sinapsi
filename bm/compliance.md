---
title: compliance
type: note
permalink: compliance
---

## v1 industrial standards (SRS-DAQ-001 v0.3 §5.1)
- CE marking (EU market placement).
- EN 61010-1 (safety of measurement/control/lab equipment); EN 61010-2-030
  (measurement/test circuits — FE-HV/FE-CUR, see [[modules]]).
- EN 61326-1 (EMC for measurement/control); EN IEC 61131-2 (programmable
  controllers); EN 60068 series (environmental testing, see [[hardware]]).
- Directives: 2014/30/EU (EMC), 2014/35/EU (Low Voltage — mains-switching MOD-RLY,
  FE-HV), RoHS 3 (2011/65/EU + 2015/863), REACH (EC 1907/2006), WEEE (2012/19/EU).

## Roadmap standards (§5.2)
- Aerospace: RTCA DO-160G (environmental qualification), DO-254 (airborne hardware
  design assurance). Roadmap, not v1 (see [[vision]]).
- Functional safety: IEC 61508 / ISO 13849 — for the certified safety-relay variant
  (post-v1).

## Architectural constraints (§5.3)
- CON-01 — internal backbone is TSN-capable Ethernet; non-Ethernet fieldbuses only
  via gateways.
- CON-02 — application protocol on the backbone is OPC UA PubSub over TSN.
- CON-03 — configuration framework is openDAQ; alternative paths conform to the same
  information model.
- CON-04 — no proprietary hardware tools for deployment or configuration.

## Verification & validation (§6)
Each requirement is tagged Test, Analysis, Inspection, or Demonstration. A separate
V&V Plan will detail acceptance criteria, fixtures, and traceability between
requirements, test cases, and standards.
