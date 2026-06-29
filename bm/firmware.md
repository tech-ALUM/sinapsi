---
title: firmware
type: note
permalink: firmware
---

## Communication backbone (SRS-DAQ-001 v0.3 §2.1)
Layered, bottom to top: Ethernet over M12 + PoE → data-link with on-die TSN switch,
gPTP time sync, scheduled traffic → IP/UDP → OPC UA PubSub (application protocol) →
control/compute logic; an EtherCAT gateway translates outward. In a Deployment,
modules connect in any topology over two Ethernet ports, sharing one gPTP clock
(better than 1 µs) and one OPC UA PubSub bus. See [[hardware]], [[modules]].

## Real-time control loops (§2, §3.14)
- Three tiers: Tier 1 local sub-ms; Tier 2 distributed 1 ms hard real-time; Tier 3
  supervisory 10–100 Hz on MOD-CPU (see [[vision]]).
- Tier-2 cycle 1000 µs ± 50 µs, jitter ≤ 5%; 99.99th-percentile ≤ 1100 µs.
- One Loop: up to 50 channels across modules inside the 1 ms budget.
- Multiple concurrent independent Loops without interference, via TSN traffic-class
  separation and scheduled traffic.
- Each Loop has a Master and ≥1 Backup, assigned at config time. On Master failure
  the Backup takes over within 500 µs of detection; Loop state replicated
  Master→Backup with sub-cycle latency.
- A Loop keeps running if its own hardware is intact, regardless of other failures.
- Tier-1 local loops target ≤ 250 µs.

## Network and topology (§3.15)
- ≥2 Ethernet ports per module with integrated TSN switching; daisy-chain, star,
  tree, ring, mesh — no external switch.
- gPTP (802.1AS) sync better than 1 µs between any two modules in a domain.
- One sync domain: up to 64 modules at full real-time performance. A Deployment:
  up to 256 modules across up to 8 domains.
- FRER (802.1CB) replication in ring/mesh; single-link recovery within 10 ms.

## Failure detection and resilience (§3.16)
- Catastrophic failure (module stops forwarding) detected within 10 ms via combined
  loss of gPTP sync and absent downstream traffic; redundant topology reconfigures
  within 10 ms.
- Non-catastrophic failure (forwards traffic, app logic compromised) of a
  Loop-sharing module detected within 1 ms via OPC UA PubSub heartbeat/watchdog.
- One module's failure must not break communication among the rest.
- Modules publish a structured fault report (timestamp, class, subsystem).

## External communication (§3.17)
- v1: EtherCAT gateway — system appears as an EtherCAT slave or coordinates with
  external EtherCAT masters.
- Architecture keeps extensibility to CAN, Modbus, OPC UA Client/Server, PROFINET,
  EtherNet/IP; those gateways are post-v1 (see [[tasks]]).

## Configuration (§3.18)
- Configured through openDAQ; no proprietary hardware — a standard PC with a
  standard NIC suffices.
- Each module self-describes via the OPC UA information model and companion specs.
- Automatic discovery of newly attached modules; each module exposes a local HTTPS
  setup/diagnostics interface.

## Buffering, firmware lifecycle, security, diagnostics (§3.20–3.23)
- Each I/O module buffers ≥1 s of full-rate data against transient link loss; on
  restoration it re-publishes with original timestamps (5 s under question, see
  [[tasks]]).
- OTA firmware update over OPC UA; images cryptographically signed (invalid ones
  rejected); A/B partitioning with automatic rollback on boot failure.
- OPC UA authentication via X.509; hardware-rooted secure boot; debug interfaces
  disableable in production.
- Status LEDs (power, per-port link/activity, health, fault); non-volatile event
  log ≥1000 entries (FIFO); logs exportable via OPC UA file transfer.
