---
title: tasks
type: note
permalink: tasks
---

## Open

### Baseline items — SRS-DAQ-001 v0.3 §8 (resolve before v1 freeze)
- [ ] OPEN-01 — Final channel/connector layout per module variant. (Architecture) → [[modules]]
- [ ] OPEN-02 — Compute-module thermal design: ≤15 W continuous, passive conduction to chassis at +70 °C. (Architecture) → [[hardware]]
- [ ] OPEN-03 — Mechanical form factor: DIN-rail vs panel-mount; vibration-rated card retention. (Mechanical) → [[hardware]]
- [ ] OPEN-04 — Primary redundancy mechanism: FRER vs MRP. (Network) → [[firmware]]
- [ ] OPEN-05 — Confirm certification target with the notified body. (Compliance) → [[compliance]]
- [ ] OPEN-06 — Mezzanine connector to reserve digital + sync + ID pins for future smart (ADC-on-card) cards and v2 hot-plug. (Architecture) → [[modules]]

### Open decisions from SRS review (Alberto's comments)
- [ ] Relay module to v1, not v2: promote FR-RLY-01/02/03 to Must; keep the certified-safety variant (FR-RLY-05) in v2. → [[modules]], [[compliance]]
- [ ] Operating temperature −40/+70 °C instead of the −20/+70 °C baseline (NFR-ENV-01). → [[hardware]]
- [ ] Network connector: M12 is not settled — choose it (§2.1, NFR-CONN). → [[hardware]]
- [ ] gPTP 7-hop limit vs 50-channel loops: <1 µs sync holds within ~7 hops, but a 50-channel loop usually exceeds 7 hops — define behaviour past 7 hops and the effect on FR-LOOP-03 / FR-NET-04. → [[firmware]]
- [ ] Define "phase-coherent" for a typical application (vs competitors/standards) and the max gPTP-compatible synced sample rate; apply to MOD-DYN (FR-DYN-03) and power V/I (FR-CUR-03). → [[firmware]], [[modules]]
- [ ] Carrier output path: FEs have no output today, so the carrier can't drive outputs — add DO/AO/relay front-end cards, or route UNI excitation for DO/AO. → [[modules]]
- [ ] Two carriers as one virtual 8-channel module closing a sub-ms loop: feasibility, architecture implications, limits (FR-LOOP-08). → [[modules]], [[firmware]]
- [ ] Every device EtherCAT/openDAQ-capable standalone: each module (carrier included) should talk to third-party masters even when alone, not only via MOD-CPU (FR-EXT-02, FR-CPU-02). → [[firmware]]
- [ ] MOD-DYN sample rate: feasibility of 1 MSps vs the 100 kSps baseline (FR-DYN-02); limits and architecture impact. → [[modules]]
- [ ] Dedicated power module: does a 3×V / 3×I module at ~10 MSps make sense, or is less enough? Relatedly, keep FE-CUR's three input types together (FR-CUR-01) or split into separate cards. → [[modules]]
- [ ] Local buffering: raise ≥1 s to 5 s (FR-BUF-01) — technical feasibility. → [[firmware]]
- [ ] MOD-CPU: aux 24 V supply is a Must (FR-CPU-03, currently Should); confirm whether MOD-CPU itself may slip to v2. → [[modules]], [[hardware]]
- [ ] Excitation gaps: add standard software-selectable voltage excitation for voltage-output sensors; state explicitly that carrier excitation is routed to the FE connector (FR-CAR-03, FR-UNI). → [[hardware]], [[modules]]
- [ ] FE-HV external-divider mode (FR-HV-03): FE-card design implications; why not measure raw voltage and scale in software? → [[modules]]
- [ ] DIO requirements review: 20 kHz PWM adequacy for servos/industrial actuators (FR-DIO-03); usefulness of ≥1 kV isolation (FR-DIO-06); 24 V/0.5 A vs few-mA rationale (FR-DIO-02); transition-timestamping vs defining an output sample rate (FR-DIO-05). → [[modules]]
- [ ] Standards scope: clarify EN 61010-2-030 vs the other EN 61010 parts — why not apply -2-030 throughout (STD-03/04). → [[compliance]]
- [ ] Link-utilisation cap: is the 50% real-time ceiling (NFR-PERF-02) too conservative vs standard practice? → [[firmware]]
- [ ] FRER value: articulate what 802.1CB (FR-NET-06) actually adds to the product. → [[firmware]]
- [ ] Autonomy scope: FR-DYN-04 (autonomous module) should apply to every module. → [[modules]]

### SRS document hygiene (apply when we revise the SRS later)
- [ ] Add an acronyms/definitions list (§1.4).
- [ ] FEs on the carrier: cold-swappable, sharing one carrier shell and a generalized FE mechanical + electrical interface. → [[modules]]
- [ ] Add FE-ACC: IEPE/voltage-only, 32 kSps, BNC — alongside (not replacing) MOD-DYN. → [[modules]]
- [ ] Document device configuration: without MOD-CPU, can openDAQ change an amplifier range/mode standalone? How is a control loop programmed, and does it belong in this doc? → [[firmware]]
- [ ] Audit SHALL/SHOULD verbs against MoSCoW priority — several Should requirements use SHALL (§1.5).
- [ ] Reconcile the "Synaptic Platform" label with the defined product name "sinapsi" (deferred — you'll update the SRS).

## Done
- [x] 2026-06-29 — Re-seed the knowledge base from SRS v0.3 + logo kit; discard all prior content. → [[timeline]]
