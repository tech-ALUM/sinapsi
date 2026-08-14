System Requirements SpecificationSRS-DAQ-001 · v0.3 · Confidential

ALUM  ·  SYNAPTIC PLATFORM

**System Requirements**

**Specification**

Modular Data Acquisition & Control System

**SRS-DAQ-001**

| DOCUMENT ID | SRS-DAQ-001 |
| --- | --- |
| VERSION | 0.3 (Draft) |
| DATE | 2026-05-31 |
| STATUS | For internal review |
| FORMAT BASIS | ISO/IEC/IEEE 29148:2018 |
| CLASSIFICATION | **Confidential** |

*Confidential — internal-review draft. Distribution restricted to the project team and named reviewers.*

# Document Control

## Revision history

| **VERSION** | **DATE** | **STATUS** | **SUMMARY** |
| --- | --- | --- | --- |
| **0.3** | 2026-05-31 | **Draft** | Requirements baseline issued for internal review. |

## Approvals

| **ROLE** | **NAME** | **DATE** |
| --- | --- | --- |
| **Prepared by** |  |  |
| **Reviewed by** |  |  |
| **Approved by** |  |  |

## Reference documents

| **REF** | **DOCUMENT** | **NOTES** |
| --- | --- | --- |
| **R1** | **SRS-DAQ-001**  v0.3 (2026-05-31) | This document — the requirements baseline. |
| **R2** | **DAQ Project Handoff**  (June 2026) SUPERSEEDED | Architecture and protocol strategy; source for Figures 1–2. |

## Confidentiality

This document is confidential to the Alum / Synaptic project and is provided for internal review. It must not be distributed outside the project team and named reviewers without written authorization. All requirement IDs, priorities, verification methods, and figures derive from the reference documents listed above; no specifications beyond those sources are claimed.

**Contents**

	**Document Control**	**2**

	Revision history	2

	Approvals	2

	Reference documents	2

	Confidentiality	2

	**1.  Introduction**	**4**

	1.1  Purpose	4

	1.2  Scope	4

	1.3  Stakeholders	4

	1.4  Definitions	4

	1.5  Requirement notation	5

	**2.  System Overview**	**5**

	2.1  Communication architecture	6

	**3.  Functional Requirements**	**7**

	3.1  Product family and module lineup	7

	3.2  Carrier and front-end card architecture	8

	3.3  Universal analog input card (FE-UNI)	8

	3.4  Bridge / strain card (FE-STG)	9

	3.5  Isolated high-voltage card (FE-HV)	9

	3.6  Isolated current card (FE-CUR)	9

	3.7  Dynamic / vibration module (MOD-DYN)	10

	3.8  Thermocouple module (MOD-TC8)	10

	3.9  Analog output module (MOD-AO)	10

	3.10  Digital I/O module (MOD-DIO)	11

	3.11  Relay module (MOD-RLY)	11

	3.12  Compute module (MOD-CPU)	12

	3.13  Isolation and excitation	12

	3.14  Real-time control loops	12

	3.15  Network and topology	13

	3.16  Failure detection and resilience	13

	3.17  External communication and gateway	14

	3.18  Configuration and integration	14

	3.19  Hot-plug	14

	3.20  Local buffering	15

	3.21  Firmware lifecycle	15

	3.22  Security	15

	3.23  Diagnostics	15

	**4.  Non-Functional Requirements**	**16**

	4.1  Performance	16

	4.2  Environmental — v1 industrial baseline	16

	4.3  Power	17

	4.4  Connectors	17

	4.5  Reliability	17

	**5.  Constraints and Compliance**	**17**

	5.1  Standards — v1 industrial baseline	17

	5.2  Standards — aerospace and safety (roadmap)	18

	5.3  Architectural constraints	18

	**6.  Verification and Validation Strategy**	**18**

	**7.  Roadmap**	**18**

	**8.  Open Items**	**19**

	**Appendix A — The building of offices**	**19**

# 1.  Introduction

## 1.1  Purpose

This document specifies the functional and non-functional requirements for a family of modular data acquisition and control products (hereafter “the Product Family”) intended for distributed industrial measurement, control, and automation. It establishes the baseline against which v1 development, verification, and validation will be planned and executed.

## 1.2  Scope

The Product Family is a catalog of networked hardware modules sharing a common communication, synchronization, and configuration architecture. It is not a single fixed system: a customer composes a Deployment by selecting and interconnecting modules appropriate to the application. Module types specialize in analog input, analog output, digital I/O, relay switching, dynamic measurement, temperature measurement, and compute. Modules interconnect over a time-sensitive Ethernet fabric and are configurable via an open-source software stack without proprietary hardware. The Product Family targets industrial automation,power and generator measurement, and aerospace applications, with v1 focused on a baseline industrial profile.

## 1.3  Stakeholders

| **STAKEHOLDER** | **INTEREST** |
| --- | --- |
| **End customer (integrator)** | Reliable distributed I/O for control applications |
| **End customer (OEM)** | Embeddable subsystem with predictable lifecycle |
| **Development team** | Clear, testable requirements |
| **Certification body** | Compliance evidence against EU directives |
|  |  |

## 1.4  Definitions

| **TERM** | **DEFINITION** |
| --- | --- |
| **Product Family** | The complete catalog of module types described in this document (called *sinapsi*) |
| **Module** | A single physical unit of the Product Family (a Carrier or a self-contained module) |
| **Carrier** | A module hosting up to four interchangeable front-end cards and providing the shared ADC, MCU, network, and power |
| **Front-end card** | An interchangeable signal-conditioning card installed in a Carrier slot |
| **Deployment** | A specific network of Modules configured by a customer for an application |
| **Loop** | A closed-loop control function executed across one or more Modules within a deterministic time budget |
| **Loop Master** | The Module executing the control law of a given Loop |
| **Loop Backup** | A pre-designated Module ready to assume the Loop Master role on failure |
| **Catastrophic failure** | A failure mode in which the affected Module ceases to forward network traffic |
| **Non-catastrophic failure** | A failure mode in which the affected Module keeps forwarding traffic but its application logic is compromised |
| **Hot-plug** | Adding a Module to a running Deployment without service interruption |
| **Hot-swap** | Removing and replacing a Module without interrupting active Loops |
| **TSN** | Time-Sensitive Networking (IEEE 802.1) |
| **gPTP** | Generalized Precision Time Protocol (IEEE 802.1AS) |
| **FRER** | Frame Replication and Elimination for Reliability (IEEE 802.1CB) |
| **CJC** | Cold-Junction Compensation (thermocouple measurement) |
| **ENOB** | Effective Number of Bits |

## 1.5  Requirement notation

Each requirement carries an ID, a MoSCoW priority, and a verification method in the V column. Normative verbs follow the priority: Must uses SHALL, Should uses SHOULD, Could uses MAY, and Won’t-v1 items are descriptive.

**Priority (MoSCoW).  ****Must** — mandatory (SHALL);   **Should** — recommended (SHOULD);   **Could** — optional (MAY);   ***Won’t-v1*** — out of v1 scope (descriptive).

**Verification (V).  ****T** Test     **A** Analysis     **I** Inspection     **D** Demonstration.

# 2.  System Overview

The Product Family is built on a single architectural principle: a shared, synchronized communication backbone connects modules whose signal chains are each optimized for their job. Two module classes exist. Carriers host up to four interchangeable front-end cards that all feed one simultaneous-sampling ADC, and are the home for the family of slow analog inputs (universal, bridge, isolated voltage, isolated current). Self-contained modules exist where the signal chain is fundamentally different from the shared-ADC model: the dynamic module has its own high-rate ADC, the thermocouple module a slow multiplexed converter with cold-junction compensation, the analog-output module a DAC, the digital and relay modules their own output stages, and the compute module a processor for supervision and gateway functions.

Three tiers of control coexist:

- **Tier 1 — Local sub-ms closed-loop.** Executed on a single module for co-located sensor and actuator.

- **Tier 2 — Network-distributed 1 ms hard real-time loops.** A Loop Master coordinates up to 50 channels across multiple modules.

- **Tier 3 — Supervisory loops (10–100 Hz).** On compute modules for DSP, orchestration, vision, and optimization.

## 2.1  Communication architecture

The backbone is layered. Reading from the bottom: physical Ethernet over M12 with Power-over-Ethernet; a data-link layer with an on-die TSN switch, gPTP time 1synchronization, and scheduled traffic; standard IP and UDP; and OPC UA PubSub as the application protocol. The control and compute logic sits on top, and an EtherCAT gateway translates to the outside world.



*Figure 1 — Layered communication stack: what each protocol and chip does.*

In a Deployment, modules connect in any topology through their two Ethernet ports. They share one synchronized clock (gPTP, better than one microsecond) and one publish/subscribe data bus (OPC UA PubSub over TSN). A compute module reads the whole bus and performs supervision, and reaches external controllers through the EtherCAT gateway.

*Figure 2 — Network reference architecture of a Deployment: one gPTP clock, one OPC UA PubSub bus.*

# 3.  Functional Requirements

## 3.1  Product family and module lineup

The v1 Product Family comprises one carrier, four front-end cards, and six self-contained modules:

| **SKU** | **TYPE** | **FUNCTION** |
| --- | --- | --- |
| **AIC-4** | Carrier | 4 card slots; ADS131M04 (4ch, 24-bit, simultaneous, 32 kSps); RT1180; 2× Ethernet + TSN; PoE; TCXO |
| **FE-UNI** | Front-end card | Voltage, 4-20 mA, RTD, thermocouple (with CJC), basic IEPE |
| **FE-STG** | Front-end card | Bridge / strain, load cells, pressure, torque |
| **FE-HV** | Front-end card (isolated) | Voltage to 1000 V CAT III; external-divider input above; on-card barrier + isolated DC-DC |
| **FE-CUR** | Front-end card (isolated) | Current via shunt / CT / Rogowski; on-card barrier + isolated DC-DC |
| **MOD-DYN** | Module | Dynamic / vibration; local 100 kSps ADC; BNC; gPTP-synced |
| **MOD-TC8** | Module | 8× thermocouple; standard flat connectors (2 rows of 4); multiplexed ADC; isothermal CJC |
| **MOD-AO** | Module | 4ch ±10 V, 16-bit analog output |
| **MOD-DIO** | Module | Configurable DI / DO; DO software-selectable 24 V 0.5 A or logic-level; PWM-capable |
| **MOD-RLY** | Module (v2) | ~8 form-C relays; 250 V AC / 6 A, 30 V DC / 6 A; contact monitoring |
| **MOD-CPU** | Module | i.MX 8 compute; openDAQ host; EtherCAT gateway; supervisory loops |

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-FAM-01** | **Must** | The Product Family SHALL provide at least one module type for each of: analog input, analog output, digital input, digital output, and compute. | **I** |
| **FR-FAM-02** | **Must** | The carrier-and-card architecture SHALL allow any combination of supported front-end cards across the four slots of a single carrier. | **T** |
| **FR-FAM-03** | **Should** | New front-end card types SHOULD be addable to the catalog without redesign of the carrier. | **A** |

**RATIONALE   **Few SKUs, large combinatorial coverage. The carrier amortizes the expensive shared infrastructure (MCU, network, PoE, enclosure) across four channels, and is a platform onto which new slow-analog card types attach without new module designs. Dynamic and dedicated thermocouple measurement are separate modules because their signal chains (high-rate ADC; multiplexed slow ADC with CJC) do not fit the shared-ADC model.

## 3.2  Carrier and front-end card architecture

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-CAR-01** | **Must** | The AIC-4 carrier SHALL host up to four interchangeable front-end cards. | **I** |
| **FR-CAR-02** | **Must** | The carrier SHALL provide a single 4-channel simultaneous-sampling 24-bit ADC shared by the four card slots, with all four channels operable concurrently at up to 32 kSps without rate penalty. | **T** |
| **FR-CAR-03** | **Must** | The carrier SHALL supply regulated excitation rails to the slots sufficient for IEPE current, RTD excitation, bridge excitation, and 4-20 mA loop power. | **T** |
| **FR-CAR-04** | **Must** | The carrier SHALL detect the type of card installed in each slot and configure the signal path accordingly. | **T** |
| **FR-CAR-05** | **Should** | The card retention and connector SHALL withstand the vibration profile of NFR-ENV-05 without intermittent contact. | **T** |

**RATIONALE   **The shared ADC is one chip with four parallel converters (not a multiplexer), so simultaneous full-rate sampling on all channels is inherent, and cross-channel phase alignment (needed for power V/I pairs and multi-axis strain) comes for free.

## 3.3  Universal analog input card (FE-UNI)

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-UNI-01** | **Must** | FE-UNI SHALL support, software-selectable per channel: voltage (±10 V, ±5 V, ±1 V, ±100 mV), 4-20 mA current, RTD (PT100/PT1000, 2/3/4-wire), thermocouple (types J/K/T/E/N/R/S/B with CJC), and resistance. | **T** |
| **FR-UNI-02** | **Must** | FE-UNI SHALL support basic IEPE input (constant-current excitation with AC coupling) for general-purpose vibration. | **T** |
| **FR-UNI-03** | **Must** | FE-UNI SHALL provide an ENOB of at least 20 bits at 10 kSps in the ±10 V range. | **T** |
| **FR-UNI-04** | **Should** | Thermocouple measurement on FE-UNI SHOULD include cold-junction compensation referenced at the terminal. | **T** |

**RATIONALE   **FE-UNI is the high-volume card and the customer’s “swiss-army” channel. Basic IEPE lives here so a single card covers the common mix including general vibration; high-performance vibration is served by MOD-DYN.

## 3.4  Bridge / strain card (FE-STG)

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-STG-01** | **Must** | FE-STG SHALL support full, half, and quarter bridge configurations with on-card completion. | **T** |
| **FR-STG-02** | **Must** | FE-STG SHALL provide programmable, ratiometric bridge excitation with remote sense. | **T** |
| **FR-STG-03** | **Should** | FE-STG SHALL provide shunt-calibration capability. | **T** |



## 3.5  Isolated high-voltage card (FE-HV)

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-HV-01** | **Must** | FE-HV SHALL measure voltage up to 1000 V working, CAT III. | **T** |
| **FR-HV-02** | **Must** | FE-HV SHALL provide reinforced channel-to-channel and channel-to-ground isolation, with the isolation barrier and an isolated DC-DC located on the card. | **T** |
| **FR-HV-03** | **Should** | FE-HV SHALL support an external-divider / PT input mode for measurements above 1000 V. | **T** |
| **FR-HV-04** | **Must** | FE-HV SHALL comply with EN 61010-2-030 for measurement circuits. | **T** |

**RATIONALE   **1000 V CAT III covers all LV three-phase systems (400/690 V) with margin and matches the input rating of professional power analyzers. Above 1000 V the market uses external PTs/dividers rather than direct measurement, so direct >1000 V is intentionally out of scope to contain cost and certification burden.

## 3.6  Isolated current card (FE-CUR)

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-CUR-01** | **Must** | FE-CUR SHALL measure current via internal shunt, external current transformer, or Rogowski coil input. | **T** |
| **FR-CUR-02** | **Must** | FE-CUR SHALL provide reinforced isolation with on-card barrier and isolated DC-DC, matching FE-HV so V/I pairs form an isolated power-measurement set. | **T** |
| **FR-CUR-03** | **Should** | A Deployment SHALL be able to perform three-phase power measurement by synchronizing FE-HV/FE-CUR channels across multiple carriers via gPTP. | **T** |



## 3.7  Dynamic / vibration module (MOD-DYN)

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-DYN-01** | **Must** | MOD-DYN SHALL provide IEPE and AC-voltage inputs via BNC connectors. | **T** |
| **FR-DYN-02** | **Must** | MOD-DYN SHALL incorporate a local ADC sampling at up to 100 kSps per channel. | **T** |
| **FR-DYN-03** | **Must** | MOD-DYN’s local ADC SHALL be synchronized to the shared gPTP time domain so its samples are phase-coherent with other modules. | **T** |
| **FR-DYN-04** | **Must** | MOD-DYN SHALL connect to the network as an autonomous module (its own MCU and two Ethernet ports). | **I** |

**RATIONALE   **Dynamic measurement needs a faster ADC and a different connector than the shared-ADC carrier provides. Making MOD-DYN autonomous keeps every carrier simple and cheap, and phase coherence is achieved through the same gPTP timestamping used across the whole Deployment rather than through a more complex dual-mode carrier slot.

## 3.8  Thermocouple module (MOD-TC8)

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-TC-01** | **Must** | MOD-TC8 SHALL provide 8 thermocouple input channels using standard flat thermocouple connectors | **I** |
| **FR-TC-02** | **Must** | MOD-TC8 SHALL provide cold-junction compensation via an isothermal terminal block with dedicated temperature sensing. | **T** |
| **FR-TC-03** | **Must** | MOD-TC8 SHALL support thermocouple types J/K/T/E/N/R/S/B. | **T** |
| **FR-TC-04** | **Should** | MOD-TC8 MAY use a multiplexed low-speed, low-noise ADC, since thermocouple signals are quasi-static. | **A** |

## 3.9  Analog output module (MOD-AO)

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-AO-01** | **Must** | MOD-AO SHALL provide at least 4 channels of ±10 V output at 16-bit resolution. | **T** |
| **FR-AO-02** | **Must** | MOD-AO SHALL sustain an update rate of at least 10 kSps per channel. | **T** |
| **FR-AO-03** | **Should** | Each MOD-AO channel SHOULD source at least 10 mA. | **T** |

**RATIONALE   **Analog output uses a DAC, not the carrier’s ADC, so it is a self-contained module. Channel isolation is intentionally omitted: switching/isolation needs are met by the relay module, and AO isolation serves only niche cases not worth the per-channel cost.

## 3.10  Digital I/O module (MOD-DIO)

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-DIO-01** | **Must** | MOD-DIO SHALL provide channels software-selectable per channel as digital input or digital output. | **T** |
| **FR-DIO-02** | **Must** | Each digital output SHALL be software-selectable between 24 V industrial mode (sourcing at least 0.5 A) and logic-level mode (3.3/5 V, low current). | **T** |
| **FR-DIO-03** | **Must** | Digital outputs SHALL support PWM generation up to at least 20 kHz in 24 V industrial mode. | **T** |
| **FR-DIO-04** | **Must** | Digital outputs SHALL provide overcurrent and short-circuit protection and inductive-load demagnetization handling. | **T** |
| **FR-DIO-05** | **Must** | Digital inputs and outputs SHALL support transition timestamping at 1 µs resolution. | **T** |
| **FR-DIO-06** | **Should** | All digital channels SHOULD provide galvanic isolation of at least 1 kV. | **T** |

**RATIONALE   **0.5 A at 24 V is the industrial standard for digital outputs (solenoids, valves, stack lights), not an excess. Logic-level mode covers signalling; note that the industrial driver’s slew-rate limiting suits PWM and logic signalling, not high-speed (MHz) pulse generation.

## 3.11  Relay module (MOD-RLY)

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-RLY-01** | **Should** | MOD-RLY SHALL provide approximately 8 form-C (changeover) mechanical relay channels. | **I** |
| **FR-RLY-02** | **Should** | Each relay SHALL switch 250 V AC / 6 A and 30 V DC / 6 A, with DC capability derated at higher DC voltages. | **T** |
| **FR-RLY-03** | **Should** | Each relay channel SHALL provide contact-state monitoring and operation counting for diagnostics. | **T** |
| **FR-RLY-04** | **Could** | A solid-state relay variant MAY be offered for high-cycle or fast switching. | **A** |
| **FR-RLY-05** | *Won’t-v1* | Certified functional-safety switching (force-guided contacts, dual-channel, ISO 13849 / IEC 61508) is a separate later product, not this module. | **—** |

**RATIONALE   **The relay module is not a duplicate of MOD-DIO: a dry contact switches AC, mains, higher current, and changeover paths that a 24 V semiconductor output cannot. Key applications are generator/load-bank test automation, switching contactors and mains loads under networked deterministic control, hardware-enforced interlocks, and fail-safe de-energization. Mains switching brings the module into electrical-safety scope, which is why it is a fast-follow rather than v1 critical path.

## 3.12  Compute module (MOD-CPU)

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-CPU-01** | **Must** | MOD-CPU SHALL host the openDAQ software stack and act as supervisory controller for Tier-3 loops. | **D** |
| **FR-CPU-02** | **Must** | MOD-CPU SHALL provide the EtherCAT gateway function (FR-EXT-02). | **T** |
| **FR-CPU-03** | **Should** | MOD-CPU SHALL accept an auxiliary 24 V DC supply in addition to PoE. | **T** |

## 3.13  Isolation and excitation

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-ISO-01** | **Must** | Channel isolation SHALL be provided per-channel by isolated front-end cards, each carrying its own isolation barrier and isolated DC-DC converter. | **T** |
| **FR-ISO-02** | **Must** | Isolated and non-isolated cards SHALL be mixable within a single carrier. | **T** |
| **FR-ISO-03** | **Must** | The carrier SHALL provide the excitation rails required by installed cards without cross-channel interference. | **T** |

**RATIONALE   **Per-channel isolation on the card (the chosen option) gives true channel-to-channel isolation despite a shared ADC chip, because each isolated card’s barrier separates its hot side from the common ADC ground. Isolation is paid for only on the channels that need it.

## 3.14  Real-time control loops

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-LOOP-01** | **Must** | The system SHALL execute hard real-time control loops with a cycle time of 1000 µs ± 50 µs (jitter ≤ 5%). | **T** |
| **FR-LOOP-02** | **Must** | The 99.99th percentile loop cycle time SHALL NOT exceed 1100 µs under nominal conditions. | **T** |
| **FR-LOOP-03** | **Must** | A single Loop SHALL support up to 50 channels distributed across multiple modules within the 1 ms budget. | **T** |
| **FR-LOOP-04** | **Must** | A single Deployment SHALL host multiple concurrent independent Loops without mutual interference, enforced by TSN traffic-class separation and scheduled traffic. | **T** |
| **FR-LOOP-05** | **Must** | A Loop SHALL have a designated Loop Master and at least one designated Loop Backup, assigned at configuration time. | **I** |
| **FR-LOOP-06** | **Must** | On Loop Master failure, a Loop Backup SHALL assume the Master role within 500 µs of failure detection; Loop state SHALL be continuously replicated Master to Backup with latency below one cycle. | **T** |
| **FR-LOOP-07** | **Must** | A Loop whose required hardware remains functional SHALL continue unaffected when other Loops or modules fail. | **T** |
| **FR-LOOP-08** | **Should** | Local Tier-1 loops within a single module SHOULD achieve cycle times of 250 µs or better. | **T** |

## 3.15  Network and topology

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-NET-01** | **Must** | Each module SHALL provide at least two Ethernet ports with integrated TSN switching, supporting daisy-chain, star, tree, ring, and mesh topologies without external switching hardware. | **I/T** |
| **FR-NET-02** | **Must** | A Deployment SHALL function correctly under any physically valid interconnection of compliant modules. | **T** |
| **FR-NET-03** | **Must** | The network SHALL provide gPTP (IEEE 802.1AS) synchronization better than 1 µs between any two modules within a synchronization domain. | **T** |
| **FR-NET-04** | **Must** | A single synchronization domain SHALL support up to 64 modules with full real-time performance. | **T** |
| **FR-NET-05** | **Must** | A Deployment SHALL support up to 256 modules across up to 8 synchronization domains interconnected via TSN-capable switching. | **T** |
| **FR-NET-06** | **Should** | The network SHALL support IEEE 802.1CB (FRER) frame replication in ring and mesh topologies. | **T** |
| **FR-NET-07** | **Should** | The network SHALL recover from single-link failure in redundant topologies within 10 ms. | **T** |

## 3.16  Failure detection and resilience

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-FAIL-01** | **Must** | Each module SHALL detect catastrophic failure of any other module within 10 ms, based on combined loss of gPTP sync and absence of expected downstream traffic. | **T** |
| **FR-FAIL-02** | **Must** | Each module SHALL detect non-catastrophic failure of any module sharing a Loop within 1 ms, based on OPC UA PubSub heartbeat and data-publication watchdog. | **T** |
| **FR-FAIL-03** | **Must** | A non-catastrophic failure of one or more modules SHALL NOT compromise communication between the remaining modules. | **T** |
| **FR-FAIL-04** | **Must** | A catastrophic failure in a redundant topology SHALL NOT compromise communication between remaining modules; the network SHALL reconfigure within 10 ms. | **T** |
| **FR-FAIL-05** | **Should** | Each module SHALL publish a structured fault report (timestamp, fault class, affected subsystem). | **T** |

## 3.17  External communication and gateway

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-EXT-01** | **Must** | The architecture SHALL host gateway functions translating between internal OPC UA PubSub and external industrial protocols. | **A** |
| **FR-EXT-02** | **Must** | v1 SHALL provide an EtherCAT gateway allowing the system to appear as an EtherCAT slave or coordinate with external EtherCAT masters. | **T** |
| **FR-EXT-03** | **Should** | The architecture SHALL preserve extensibility to CAN bus, Modbus, OPC UA Client/Server, PROFINET, and EtherNet/IP. | **A** |
| **FR-EXT-04** | *Won’t-v1* | CAN, Modbus, PROFINET, and EtherNet/IP gateways are deferred to post-v1. | **—** |

## 3.18  Configuration and integration

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-CFG-01** | **Must** | The system SHALL be configurable through openDAQ. | **D** |
| **FR-CFG-02** | **Must** | Configuration SHALL require no proprietary hardware; a standard PC with a standard Ethernet NIC SHALL suffice. | **D** |
| **FR-CFG-03** | **Must** | Each module SHALL be self-describing via the OPC UA information model and applicable companion specifications. | **T** |
| **FR-CFG-04** | **Must** | The network SHALL support automatic discovery of newly attached modules. | **T** |
| **FR-CFG-05** | **Should** | Each module SHALL expose a local HTTPS web interface for setup and diagnostics. | **T** |

## 3.19  Hot-plug

Hot-plug (adding a module to a running Deployment while hard real-time loops are active) is deferred to v2. Its cost is concentrated in one firmware item — dynamic TSN schedule reconfiguration on a live network without perturbing active loops — which is removed from the v1 critical path. Dropping hot-plug does not affect topology freedom (FR-NET-01/02: any topology is still supported, fixed at startup) or fault tolerance (FR-NET-06/07: redundancy uses pre-provisioned paths and requires no dynamic reconfiguration). Swapping a front-end card inside a carrier likewise requires the carrier to be powered down.

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-HOT-01** | *Won’t-v1* | Hot-plug of modules into a running Deployment is deferred to v2; v1 Deployments are configured at startup, and topology changes require a controlled restart of the affected segment. | **—** |
| **FR-HOT-02** | **Should** | Connector and PoE inrush handling SHOULD be designed to be hot-plug-capable, so hot-plug can be enabled as a firmware upgrade in v2 without hardware change. | **A** |
|  |  |  |  |

**RATIONALE   **Hot-plug while hard real-time loops run requires dynamic TSN reconfiguration, the single highest-risk firmware item; even EtherCAT treats live topology change as a limited special feature. Keeping the hardware hot-plug-ready preserves the option as a later firmware-enabled upgrade without committing v1 schedule to it.

## 3.20  Local buffering

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-BUF-01** | **Must** | Each I/O module SHALL buffer at least 1 second of acquired data at full rate to absorb transient link loss. | **T** |
| **FR-BUF-02** | **Should** | Buffered data SHALL be re-published on link restoration with original timestamps preserved. | **T** |

## 3.21  Firmware lifecycle

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-FW-01** | **Must** | Each module SHALL support over-the-network firmware update via OPC UA. | **T** |
| **FR-FW-02** | **Must** | Firmware images SHALL be cryptographically signed; modules SHALL reject unsigned or invalid images. | **T** |
| **FR-FW-03** | **Must** | Each module SHALL implement A/B partitioning with automatic rollback on boot failure. | **T** |

## 3.22  Security

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-SEC-01** | **Must** | Inter-module communication SHALL support OPC UA authentication via X.509 certificates. | **T** |
| **FR-SEC-02** | **Must** | Each module SHALL implement hardware-rooted secure boot. | **T** |
| **FR-SEC-03** | **Must** | Hardware debug interfaces SHALL be disableable in production. | **I** |

## 3.23  Diagnostics

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **FR-DIAG-01** | **Must** | Each module SHALL provide status LEDs for power, per-port link, per-port activity, health, and fault. | **I** |
| **FR-DIAG-02** | **Must** | Each module SHALL maintain a non-volatile event log of at least 1000 entries (FIFO). | **T** |
| **FR-DIAG-03** | **Should** | Diagnostic logs SHALL be exportable via OPC UA file transfer. | **T** |

# 4.  Non-Functional Requirements

## 4.1  Performance

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **NFR-PERF-01** | **Must** | Worst-case sensor-to-actuator latency across a 50-channel distributed Loop SHALL NOT exceed 1100 µs at the 99.99th percentile. | **T** |
| **NFR-PERF-02** | **Must** | Real-time traffic SHALL NOT exceed 50% of physical link capacity at maximum module count. | **A** |
| **NFR-PERF-03** | **Should** | Module boot time to network participation SHALL NOT exceed 10 s. | **T** |

## 4.2  Environmental — v1 industrial baseline

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **NFR-ENV-01** | **Must** | Operating temperature: −20 °C to +70 °C. | **T** |
| **NFR-ENV-02** | **Must** | Storage temperature: −40 °C to +85 °C. | **T** |
| **NFR-ENV-03** | **Must** | Operating humidity: 5% to 95% non-condensing. | **T** |
| **NFR-ENV-04** | **Must** | Ingress protection: IP54 (dust-protected, splash-resistant from any direction). | **T** |
| **NFR-ENV-05** | **Must** | Vibration per EN 60068-2-6 and EN 60068-2-64, industrial-vehicle profile. | **T** |
| **NFR-ENV-06** | **Must** | Shock per EN 60068-2-27. | **T** |
| **NFR-ENV-07** | **Must** | Cooling SHALL be passive (no fans or vents), to preserve the IP54 sealed enclosure. | **A** |
| **NFR-ENV-08** | **Should** | Sustained operation at +70 °C ambient under direct solar load. | **T** |

**RATIONALE   **Passive cooling is mandatory because fans require vents, which break the IP54 rating. Heat is managed by conduction to a metal enclosure, heat-spreading, and industrial-temperature component selection. The compute module is the principal thermal watch item.

## 4.3  Power

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **NFR-PWR-01** | **Must** | I/O modules SHALL support PoE per IEEE 802.3bt Type 3 (up to 60 W). | **T** |
|  |  |  |  |
| **NFR-PWR-03** | **Should** | All DC inputs SHALL have reverse-polarity protection and transient suppression per IEC 61000-4-5. | **T** |

## 4.4  Connectors

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
|  |  |  |  |
| **NFR-CONN-02** | **Should** | Signal connectors SHALL be keyed and positively locking against vibration. | **I** |

## 4.5  Reliability

| **ID** | **PRIORITY** | **REQUIREMENT** | **V** |
| --- | --- | --- | --- |
| **NFR-REL-01** | **Must** | Target MTBF per module: ≥ 100,000 hours at 40 °C ambient. | **A** |
|  |  |  |  |

# 5.  Constraints and Compliance

## 5.1  Standards — v1 industrial baseline

| **ID** | **STANDARD** | **SCOPE** |
| --- | --- | --- |
| **STD-01** | **CE Marking** | Required for EU market placement |
| **STD-02** | **EN 61010-1** | Safety of measurement, control, and laboratory equipment |
| **STD-03** | **EN 61010-2-030** | Measurement and test circuits (FE-HV / FE-CUR) |
| **STD-04** | **EN 61326-1** | EMC for measurement and control equipment |
| **STD-05** | **EN IEC 61131-2** | Programmable controllers — equipment requirements |
| **STD-06** | **EN 60068 series** | Environmental testing |
| **STD-07** | **Dir. 2011/65/EU + 2015/863** | RoHS 3 |
| **STD-08** | **Reg. EC 1907/2006** | REACH |
| **STD-09** | **Dir. 2012/19/EU** | WEEE |
| **STD-10** | **Dir. 2014/30/EU** | EMC Directive |
| **STD-11** | **Dir. 2014/35/EU** | Low Voltage Directive (mains-switching MOD-RLY, FE-HV) |

## 5.2  Standards — aerospace and safety (roadmap)

| **ID** | **STANDARD** | **SCOPE** |
| --- | --- | --- |
| **STD-AERO-01** | **RTCA DO-160G** | Environmental qualification for airborne equipment |
| **STD-AERO-02** | **RTCA DO-254** | Airborne electronic hardware design assurance (criticality-dependent) |
| **STD-SAFE-01** | **IEC 61508 / ISO 13849** | Functional safety (certified safety relay variant) |

## 5.3  Architectural constraints

| **ID** | **CONSTRAINT** |
| --- | --- |
| **CON-01** | Internal backbone is TSN-capable Ethernet; non-Ethernet fieldbuses are reached only via gateways. |
| **CON-02** | Application protocol on the backbone is OPC UA PubSub over TSN. |
| **CON-03** | Configuration framework is openDAQ; alternative paths conform to the same information model. |
| **CON-04** | No proprietary hardware tools required for deployment or configuration. |

# 6.  Verification and Validation Strategy

Each requirement is tagged with a verification method: Test (empirical measurement against an acceptance criterion), Analysis (documented reasoning or simulation), Inspection (visual or documentary check), or Demonstration (operational exercise). A separate Verification and Validation Plan will detail acceptance criteria, fixtures, and traceability between requirements, test cases, and standards.

# 7.  Roadmap

**v1 — current scope**

- AIC-4 carrier with FE-UNI, FE-STG, FE-HV, FE-CUR cards

- MOD-DYN, MOD-TC8, MOD-AO, MOD-DIO, MOD-CPU modules

- TSN + gPTP + OPC UA PubSub backbone

- 1 ms hard real-time loops, 50 channels, Master/Backup failover

- EtherCAT gateway; any static topology with FRER/ring redundancy

- Industrial baseline (IP54, passive cooling, −20/+70 °C)

- openDAQ configuration; up to 256 modules

**v2 — planned**

- MOD-RLY relay module (fast-follow after safety scoping)

- Hot-plug (live module addition) and full hot-swap

- Additional gateways (CAN, Modbus, PROFINET, EtherNet/IP)

- Non-isolated low-cost card variants for lab use

- Dynamic-carrier or smart-card option if mixed dynamic channels are demanded

**v3 — roadmap**

- Aerospace-qualified variants (DO-160G)

- Certified functional-safety relay variant (IEC 61508 / ISO 13849)

# 8.  Open Items

To be resolved before v1 freeze.

| **ID** | **ITEM** | **OWNER** | **TARGET** |
| --- | --- | --- | --- |
| **OPEN-01** | Final channel/connector layout per module variant | Architecture | Pre-PRD |
| **OPEN-02** | Compute module thermal design: target ≤ 15 W continuous dissipation, passive conduction to chassis at +70 °C ambient | Architecture | Pre-PRD |
| **OPEN-03** | Mechanical form factor; DIN-rail vs panel-mount; vibration-rated card retention | Mechanical | Pre-PRD |
| **OPEN-04** | FRER vs MRP as primary redundancy mechanism | Network | Pre-PRD |
| **OPEN-05** | Certification target confirmation with notified body | Compliance | Pre-PRD |
| **OPEN-06** | Mezzanine connector must reserve digital + sync + ID pins for future smart (ADC-on-card) cards and v2 hot-plug | Architecture | Pre-PRD |

# Appendix A — The building of offices

A plain-language tour of the components chosen so far. Imagine the whole system as a building full of offices connected by an internal mail system; each part is a person or an object with a job.

| **COMPONENT** | **IN PLAIN LANGUAGE** |
| --- | --- |
| **RT1180** *the reparto worker* | The microcontroller on every I/O module. Not a genius, but extremely fast and punctual: it measures, does a little arithmetic, decides, and acts within a millisecond, a thousand times a second, without ever getting distracted. It even has a small traffic cop built in (the TSN switch) to manage the post at its door. |
| **On-die TSN switch** *the traffic cop* | A switch baked into the worker’s own chip. It stands at the door and decides who passes and exactly when, to the microsecond, so the urgent control-loop letters always get a green light at the right instant while everything else waits its turn. Because it is on-chip, no external switch box is needed to chain modules. |
| **gPTP** *the obsessive clockmaker* | Walks through every office and synchronizes every clock to under a microsecond, continuously. It looks like a mania, but it is exactly what lets twenty different offices say “now” and mean the very same instant — the precondition for coordinating a control loop spread across modules. |
| **TCXO** *the heartbeat* | A crystal that ticks at a rock-steady frequency even when the room gets hot or cold. It is the reference both the surveyor and the clockmaker take their timing from, and the reason the measurements do not smear. |
| **ADS131M04** *the four-eyed surveyor* | The analog-to-digital converter on the carrier. It is really four surveyors in one body who all take their reading at exactly the same instant, so the four channels stay in step — which matters when you compare a voltage against a current for power, or three axes of a vibration. It is not a single surveyor running between four windows; that distinction is why all four channels can run full speed at once. |
| **Front-end cards** *the interchangeable lenses* | Small specialist attachments you click into the carrier’s four slots. Each knows how to talk to one kind of sensor — a thermocouple, a strain bridge, a high-voltage line — and hands the surveyor a clean, normalized signal. You only buy the lenses you need, and new ones can be made later without rebuilding the camera. |
| **Isolation barrier + isolated DC-DC** *the moat and drawbridge* | On the high-voltage and current cards, a moat separates the dangerous, high-potential side from the rest of the building, and a little isolated power supply acts as a drawbridge that carries energy across the moat without letting the danger through. This is what lets you measure two generator phases hundreds of volts apart on the same module without them shorting together. |
| **OPC UA PubSub** *the common language and the bulletin board* | Instead of phoning each other one by one, every office writes its data on a shared bulletin board (“I read 4.2 volts on channel 2”) and anyone interested subscribes to what they need. It is also the language the system already speaks through openDAQ. |
| **PoE** *power through the mail tube* | Power-over-Ethernet delivers electricity down the very same cable that carries the post, so each office is powered and connected by one wire instead of two. |
| **M12 connector** *the rugged doorway* | A threaded, locking industrial doorway for the network cable that does not rattle loose on a tractor and keeps splashes out. |
| **i.MX 8** *the manager* | The processor in the compute module. It reads the whole bulletin board and makes the slower, big-picture decisions — signal processing, orchestration, optimization — while the reparto workers handle the fast local jobs. |
| **EtherCAT gateway** *the front-desk translator* | Sits at reception and talks to the outside world in EtherCAT, so the building can be plugged into a factory that speaks a different language. |
| **openDAQ** *the org chart* | The open-source framework you use to decide who does what — to describe, configure, and read the whole building from any ordinary PC, with no special hardware key. |
