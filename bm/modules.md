---
title: modules
type: note
permalink: modules
---

## Two architectural patterns (SRS-DAQ-001 v0.3 §3.1–3.2)
- Carrier + interchangeable front-end cards — for the family of slow analog
  inputs. The carrier (AIC-4) amortizes the expensive shared infrastructure
  (ADC, MCU, network, PoE, enclosure) across four slots; new slow-analog card
  types attach without a new module design.
- Self-contained module — used only where the signal chain doesn't fit the
  shared-ADC model (high-rate ADC; multiplexed slow ADC with CJC; DAC; output
  stages; processor).

## v1 lineup
Carrier:
- AIC-4 — 4 card slots; shared ADS131M04 (4ch, 24-bit, simultaneous, 32 kSps);
  RT1180; 2× Ethernet + TSN; PoE; TCXO. See [[hardware]].

Front-end cards:
- FE-UNI — universal: V, 4-20 mA, RTD, thermocouple (CJC), basic IEPE. The
  high-volume "swiss-army" channel; ≥20-bit ENOB at 10 kSps on ±10 V.
- FE-STG — bridge/strain: full/half/quarter with on-card completion, ratiometric
  excitation, remote sense, shunt-cal.
- FE-HV — isolated voltage to 1000 V CAT III; on-card barrier + isolated DC-DC;
  external-divider mode above 1000 V; EN 61010-2-030.
- FE-CUR — isolated current via shunt/CT/Rogowski; isolation matched to FE-HV so
  V/I pairs form an isolated power-measurement set.

Self-contained modules:
- MOD-DYN — dynamic/vibration; local 100 kSps ADC; BNC; gPTP-synced for phase
  coherence with the rest of the Deployment.
- MOD-TC8 — 8× thermocouple; isothermal CJC; multiplexed low-speed ADC.
- MOD-AO — 4ch ±10 V, 16-bit, ≥10 kSps; channel isolation deliberately omitted.
- MOD-DIO — per-channel DI/DO; DO selectable 24 V/0.5 A or logic-level;
  PWM ≥20 kHz; 1 µs transition timestamping.
- MOD-CPU — i.MX 8; hosts openDAQ; EtherCAT gateway; Tier-3 supervision.
- MOD-RLY — ~8 form-C relays, 250 V AC/6 A, 30 V DC/6 A; contact monitoring.
  SRS marks it v2 pending electrical-safety scoping; flagged for v1 (see [[tasks]]).

## Why the split
Few SKUs, large combinatorial coverage. Dynamic and dedicated thermocouple
measurement are separate modules because their signal chains don't fit the shared
ADC. See [[firmware]] for how modules coordinate, [[compliance]] for the safety
scope on FE-HV / FE-CUR / MOD-RLY.
