---
title: vision
type: note
permalink: vision
---

## What sinapsi is
A catalog of networked, modular data-acquisition and control modules sharing one
communication, synchronization, and configuration architecture. Not a fixed
system: a customer composes a Deployment by selecting and interconnecting the
modules an application needs. (SRS-DAQ-001 v0.3 §1.1–1.2)

## The single principle
One synchronized communication backbone connects modules whose signal chains are
each optimized for their job. Carriers host up to four interchangeable front-end
cards feeding one simultaneous-sampling ADC; self-contained modules exist only
where the signal chain doesn't fit the shared-ADC model. See [[modules]], [[hardware]].

## Three tiers of control
- Tier 1 — local sub-ms loop on a single module (co-located sensor and actuator).
- Tier 2 — network-distributed 1 ms hard real-time loop; a Loop Master coordinates
  up to 50 channels across modules. See [[firmware]].
- Tier 3 — supervisory loops (10–100 Hz) on the compute module: DSP, orchestration,
  vision, optimization.

## Markets and v1 focus
Targets industrial automation, power/generator measurement, and aerospace. v1 is
the industrial baseline (IP54, passive cooling, −20/+70 °C per the SRS); aerospace
and certified functional safety are roadmap. See [[compliance]].
