---
title: hardware
type: note
permalink: hardware
---

## Shared silicon (SRS-DAQ-001 v0.3 §3.1, App. A)
- RT1180 — the MCU on every I/O module. Real-time measure-compute-decide-act
  within the 1 ms budget; integrates the on-die TSN switch.
- ADS131M04 — the carrier's ADC: 4-channel, 24-bit, simultaneous-sampling,
  32 kSps. Four parallel converters (not a multiplexer), so all four channels run
  full-rate at once and stay phase-aligned — needed for power V/I pairs and
  multi-axis strain. See [[modules]] (AIC-4).
- On-die TSN switch — baked into the RT1180; schedules traffic to the microsecond
  so control-loop frames get priority. No external switch to chain modules.
- TCXO — temperature-compensated reference oscillator; the stable timebase the ADC
  and gPTP both derive from. See [[firmware]].
- i.MX 8 — the compute-module processor (MOD-CPU): supervision, DSP, orchestration,
  the openDAQ host, and the EtherCAT gateway.

## Isolation and excitation (§3.3, §3.13)
- Per-channel isolation lives on the isolated front-end cards (FE-HV, FE-CUR):
  each carries its own barrier and isolated DC-DC. True channel-to-channel
  isolation despite the shared ADC ground, paid for only where needed.
- Isolated and non-isolated cards mix in one carrier.
- The carrier supplies regulated excitation rails to the slots — IEPE current, RTD
  and bridge excitation, 4-20 mA loop power — without cross-channel interference.

## Connectors (§2.1, §3.7–3.8, §4.4)
- Network: M12 with PoE — power and data on one cable. Connector not yet frozen,
  see [[tasks]].
- MOD-DYN: BNC. MOD-TC8: standard flat thermocouple connectors (2 rows of 4).
- Signal connectors keyed and positively locking against vibration.

## Power (§4.3)
- I/O modules powered by PoE per IEEE 802.3bt Type 3 (up to 60 W).
- MOD-CPU also accepts an auxiliary 24 V DC supply.
- All DC inputs: reverse-polarity protection and transient suppression
  (IEC 61000-4-5).

## Environmental — v1 industrial baseline (§4.2)
- Operating −20/+70 °C; storage −40/+85 °C; humidity 5–95% non-condensing.
- IP54; passive cooling only — fans would need vents and break IP54. Heat managed
  by conduction to a metal enclosure and industrial-temperature parts; the compute
  module is the principal thermal watch item (see [[tasks]], OPEN-02).
- Vibration EN 60068-2-6 / -2-64 (industrial-vehicle profile); shock EN 60068-2-27.

## Performance & reliability (§4.1, §4.5)
- Worst-case sensor-to-actuator latency across a 50-channel loop ≤ 1100 µs at the
  99.99th percentile (see [[firmware]]).
- Real-time traffic ≤ 50% of link capacity at max module count.
- Boot to network participation ≤ 10 s. Target MTBF ≥ 100,000 h at 40 °C.
