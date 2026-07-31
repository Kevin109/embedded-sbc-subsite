---
title: "How to Turn Product Requirements into an SBC Specification"
seo_title: "How to Create an Embedded SBC Specification from a PRD"
description: "Turn a PRD into a testable embedded SBC specification covering compute, I/O, power, thermal, mechanical, BSP, lifecycle, and production."
date: 2026-07-27
lastmod: 2026-07-27
draft: false
roadmap_id: "ESB-P003"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["SBC specification", "embedded SBC requirements", "single board computer specification", "SBC selection requirements", "embedded product PRD", "custom SBC specification"]
cover:
  image: "/images/posts/product-requirements-sbc-specification-hero.jpg"
  alt: "Photorealistic engineering desk for turning product requirements into an SBC specification"
images:
  - "/images/posts/product-requirements-sbc-specification-hero.jpg"
  - "/images/posts/embedded-sbc-evaluation-board-photo.webp"
  - "/images/posts/embedded-linux-build-system-hardware-photo.webp"
---

A product requirements document describes what a device should do. An SBC specification describes the board-level conditions that make those outcomes possible—and the tests that prove the conditions have been met.

The conversion is where many embedded projects lose discipline. “Seven-inch touch screen, fast processor, Wi-Fi, and industrial temperature” looks like a usable brief, but it leaves the supplier and engineering team to guess about display timing, touch interface, CPU load, network recovery, power transients, enclosure heat, operating system ownership, update strategy, and lifecycle.

A good SBC specification removes those guesses. It is specific enough to screen boards and suppliers, but it does not prescribe a component before the workload has been measured. It separates mandatory requirements from preferences, records how each requirement will be verified, and follows the board through prototype, validation, and production.

This guide turns a PRD into that decision record. It builds on the broader [embedded product requirements process](/posts/embedded-product-requirements-specification/) and focuses specifically on single-board computers, system-on-modules, and custom embedded boards.

## Start With Outcomes, Not Part Numbers

Do not begin the specification with “RK3588, 8 GB RAM, and 64 GB eMMC.” That is already a solution. Begin with an operational statement:

> The device shall display the production dashboard, collect data from two isolated RS485 networks, forward selected records over Ethernet, retain seven days of local data, and recover automatically after an uncontrolled power loss.

That sentence can be decomposed and tested. It creates questions about UI workload, serial throughput, network buffering, storage writes, clock accuracy, filesystem behavior, and recovery time. A processor name does not.

Use three requirement levels:

- **Must:** the product cannot ship without it.
- **Should:** important, but a documented compromise is possible.
- **Could:** useful option that must not force cost or risk into the base design.

Keep wishes out of the “must” column. Every unnecessary mandatory interface reduces the number of viable boards and can force a larger SoC, PCB, enclosure, or power supply.

## A Board Photo Is Not a Specification

<figure style="margin:1.5rem 0;text-align:center">
  <img
    src="/images/posts/embedded-sbc-evaluation-board-photo.webp"
    alt="Real ARM evaluation board with headers, debug connectors and mounting holes"
    loading="lazy"
    decoding="async"
    style="max-width:100%;height:auto;border-radius:12px;box-shadow:0 6px 18px rgba(0,0,0,.08)">
  <figcaption style="color:#555;margin-top:.55rem">
    Original board photo from the site archive. Headers, mounting holes, debug access, and visible connectors help with screening, but the photo cannot confirm electrical limits, pin multiplexing, BSP support, thermal behavior, or lifecycle.
  </figcaption>
</figure>

A product manager may see Ethernet, USB, and headers and conclude that a board “has all the interfaces.” An engineer must ask:

- What voltage and protection exist at each connector?
- Are the interfaces available at the same time?
- Is the UART logic level, RS232, or RS485?
- Does the USB port supply enough current during startup?
- Is the display interface validated at the required timing?
- Are mounting holes accessible after cables are installed?
- Does the Linux or Android BSP support the exact peripheral?
- Can the supplier reproduce the board revision two years later?

This is why [embedded SBC selection](/embedded-sbc/) must include hardware, software, mechanical, production, and commercial evidence.

## Step 1: Write the Operational Profile

The operational profile describes how the device behaves over a normal day and during abnormal events. It becomes the input for performance, storage, power, and reliability budgets.

Record:

- Main product functions
- Workload active at boot, idle, normal use, and peak use
- Display brightness and UI animation
- Camera count, resolution, frame rate, and duty cycle
- Network traffic and offline duration
- Sensor and fieldbus message rates
- Local database and log write rate
- User response-time target
- Boot-to-ready target
- Expected uptime and maintenance window
- Ambient temperature, airflow, dust, moisture, and vibration
- Power source and power-loss behavior
- Service access and technician skill level

Avoid adjectives. Replace “fast boot” with “the main UI shall accept touch input within 12 seconds of power application at 25°C.” Replace “reliable storage” with a write workload, retention period, power-loss test, and service life.

## Step 2: Convert Features into an Interface Matrix

Create one row for every external and internal connection.

| Function | Electrical/interface requirement | Quantity | Peak load or bandwidth | Cable/environment | Verification |
|---|---|---:|---:|---|---|
| Main display | LVDS, 1024×600 at specified timing | 1 | Pixel clock from panel datasheet | 200 mm internal cable | 72-hour pattern/UI test |
| Touch | USB 2.0 or I2C with interrupt/reset | 1 | Low | Internal, near display cable | Cold boot, ESD, 10,000-touch test |
| Machine network | Isolated RS485, 2-wire half-duplex | 2 | 115.2 kbit/s each | 100 m industrial cable | BER/noise and protocol soak |
| Plant network | 10/100/1000 Ethernet | 2 | 300 Mbit/s combined target | Shielded external cable | Throughput and link-recovery test |
| Service | USB 2.0 host | 1 | 5 V, 900 mA peak | External | Enumeration and overload test |
| Control | CAN FD, isolated | 1 | 2 Mbit/s data phase | External harness | Error injection and recovery |

The matrix prevents hidden adapters. If the PRD says “two serial ports,” decide whether they are UART, RS232, or RS485 before board selection. Our [industrial serial and Ethernet interface planning](/posts/rs485-can-ethernet-interface-planning/) guide covers termination, isolation, protection, and cabling concerns that a connector count misses.

Add three columns that teams often omit:

1. **Simultaneous use:** which ports run together?
2. **Pin/PHY conflict:** which interfaces share silicon resources?
3. **BSP evidence:** which kernel driver, device tree node, and tested board image support it?

## Step 3: Build a Compute Budget

Do not translate “responsive UI” directly into core count. Run a representative workload on one or more candidate boards and keep the test conditions identical.

Measure:

- CPU utilization per core and total
- p50 and p95 response latency
- GPU load and frame drops
- Memory working set and peak
- Storage read/write latency
- Network and peripheral interrupt load
- Temperature and frequency throttling
- Application behavior during OTA, logging, and database maintenance

A practical acceptance target is to leave engineering margin rather than optimize the prototype to 100%:

- Sustained CPU below roughly 70% under the defined peak scenario
- No critical core pinned at 100% for the product’s response window
- Peak memory plus at least 25–30% growth margin
- Storage queue and UI latency within limit while logs and updates run
- No thermal throttling that violates application latency

These are starting guardrails, not universal standards. A deterministic control workload may need more headroom. A batch-processing device may safely use all cores for short periods.

For Rockchip designs, use a factual [RK3568, RK3576, and RK3588 selection comparison](/posts/rk3568-vs-rk3576-vs-rk3588-embedded-products/) to screen processor classes, then benchmark the exact board.

## Step 4: Size Memory and Storage from Workloads

### RAM

List memory consumers separately:

- Kernel and base services
- Graphics buffers and compositor
- Browser or Qt application
- Camera buffers
- AI model and runtime
- Database cache
- Containers
- Update agent
- Diagnostic capture
- Future feature allowance

Use measured peak proportional set size where possible. Avoid choosing 8 GB because competitors advertise 8 GB. Soldered memory affects cost for every unit and usually cannot be upgraded.

### Non-volatile storage

Storage capacity must include more than application files:

| Storage consumer | Required space |
|---|---:|
| Bootloader and recovery | Fixed by layout |
| A/B operating system slots | 2 × maximum future image size |
| Application/data partition | Current data plus growth |
| Logs and crash dumps | Daily rate × retention, with cap |
| Update download/staging | Largest signed update plus margin |
| Factory and calibration data | Small but protected and backed up |
| Filesystem free-space reserve | Typically 15–25%, workload dependent |

Then calculate writes per day. Database journaling, camera snapshots, and verbose logs can dominate endurance. The [eMMC and NVMe reliability checklist](/posts/embedded-sbc-storage-reliability/) explains why advertised capacity alone is not a storage specification.

State the recovery requirement:

- The system shall boot after power removal during idle, database write, and log rotation.
- The active image shall remain bootable after power is removed during an update.
- Corrupted user data shall not prevent entry into service or recovery mode.

## Step 5: Specify Power as an Envelope

“12 V input” is incomplete. Define:

- Nominal input
- Continuous operating range
- Transient range and duration
- Reverse-polarity behavior
- Surge, EFT, and ESD environment
- Maximum steady current
- Startup/inrush current
- USB and peripheral output budget
- Brownout threshold and shutdown behavior
- Grounding and isolation
- Connector and fuse requirements

Build a first-pass power budget:

```text
Pinput =
  SBC peak power
  + display and backlight
  + USB peripheral allowance
  + camera and wireless peaks
  + field I/O and relays
  + conversion losses
  + design margin
```

Do not use the board’s idle current. Display startup, USB enumeration, radio transmission, storage writes, and CPU/GPU load can overlap. A 20–30% system margin is a useful early target, then replace estimates with measurements.

The [SBC power input design checklist](/posts/embedded-sbc-power-input-design/) covers reverse protection, brownout, grounding, and fault behavior in more depth.

## Step 6: Make Thermal and Mechanical Requirements Measurable

Specify the environment and the geometry together:

- Ambient operating and storage temperature
- Airflow: natural convection, forced air, or sealed
- Board orientation
- Maximum enclosure internal temperature
- Maximum permitted surface temperature
- SoC throttling and application-latency limits
- Heat-spreader contact area and tolerance
- PCB outline and keep-out zones
- Mounting-hole location and fastener type
- Connector direction, cable bend radius, and removal clearance
- Display, camera, antenna, battery, and speaker placement

“Fanless” is not a thermal requirement. A useful requirement is:

> At 50°C ambient in the closed production enclosure, the device shall run the peak workload for four hours without application latency exceeding 250 ms, storage exceeding its rated temperature, or the SoC entering a frequency state that violates throughput.

Mechanical fit should be checked with a 3D model or dimensioned drawing, not a product-page photograph. Connector bodies, mating plugs, cable latches, and bend radius often need more volume than the board.

## Step 7: Specify the BSP and Maintenance Contract

The software specification must name deliverables, not just “Linux supported.”

Require:

- Bootloader, kernel, and root filesystem/build-system versions
- Source repositories and all board patches
- Reproducible build instructions
- Device tree for every board revision
- GPU, VPU, ISP, NPU, Wi-Fi, and other firmware/runtime versions
- Factory flashing and recovery tools
- Secure boot and signed-update capability
- Update layout, rollback, and health-confirmation behavior
- Security advisory and patch process
- Debug interface and production-lock policy
- License manifest and SBOM output

If the team has not selected the image framework, use the [Yocto and Buildroot production trade-offs](/posts/yocto-vs-buildroot-production-embedded-linux/) to align the build system with product variants, update frequency, and maintenance capacity.

Write acceptance language:

> A clean CI worker shall rebuild a bit-identical or documented reproducible release from pinned sources and produce the factory image, update bundle, SDK, license manifest, and SBOM without unpublished files.

That requirement is much more valuable than “Yocto preferred.”

## Step 8: Add Production and Service Requirements

An SBC can pass engineering tests and still fail as a product platform if the factory and service workflows were omitted.

Specify:

- Maximum flashing time per unit
- Gang-programming or network-flashing needs
- Serial number, MAC address, certificate, and calibration programming
- Test pads, debug connector, and recovery access
- Automated test coverage for every external interface
- Board-revision identification available to software
- Factory log format and traceability database
- Field log export and remote diagnostic capability
- Replaceable storage, battery, fan, or fuse policy
- RMA data handling and secure erase

The [embedded Linux factory programming workflow](/posts/factory-flashing-workflow-embedded-linux/) provides a useful bridge from image output to a controlled production station.

## The SBC Specification Table

Use one source-of-truth table. Every row should have an owner and a verification method.

| ID | Priority | Requirement | Rationale | Verification | Evidence/status |
|---|---|---|---|---|---|
| PERF-01 | Must | UI touch response p95 ≤150 ms during database sync | Operator usability | Automated touch-to-frame measurement | Open |
| DISP-01 | Must | Drive panel ABC at its documented LVDS timing | Selected enclosure/display | 72-hour visual and signal test | Open |
| NET-02 | Must | Recover both Ethernet links within 10 s after switch reboot | Unattended operation | 500-cycle link test | Open |
| PWR-03 | Must | Boot normally from 9–36 V DC input | Vehicle/industrial supply | Programmable supply sweep | Open |
| THM-01 | Must | Meet PERF-01 at 50°C ambient in closed enclosure | Installation environment | Four-hour chamber test | Open |
| BSP-02 | Must | Rebuild release from pinned sources on clean CI worker | Long-term maintenance | Release-reproduction audit | Open |
| LIFE-01 | Must | Supplier provides PCN/EOL notification process | Five-year production plan | Contract/document review | Open |
| COST-01 | Should | Board BOM target ≤ agreed cost at volume | Product margin | Formal supplier quotation | Open |

Good requirements have five qualities:

1. **Necessary:** linked to a product outcome or risk.
2. **Unambiguous:** interpreted the same way by product, hardware, software, test, and supplier teams.
3. **Feasible:** achievable within technology, budget, and schedule.
4. **Verifiable:** has an inspection, analysis, demonstration, or test.
5. **Traceable:** linked to its source and later to a validation result.

## Worked Example: Industrial HMI Gateway

Assume the PRD says:

> A wall-mounted device displays machine status, reads two RS485 networks, synchronizes data to the plant server, and continues logging for 24 hours during network loss.

Translate it into a screening specification:

### Compute and memory

- 7-inch 1024×600 UI at 30 fps without visible input lag
- Database, two protocol services, VPN, update agent, and UI active together
- p95 touch response below 150 ms
- 2 GB RAM minimum after measured peak plus 30% margin; 4 GB preferred if a browser runtime is used

### Storage

- eMMC, not removable microSD, for the base product
- Two 1.5 GB operating-system slots with growth allowance
- 24-hour offline queue plus seven-day diagnostic retention
- Power-loss-safe database and 500-cycle interruption test

### Interfaces

- Two isolated RS485 ports with defined termination
- Two Gigabit Ethernet ports with independent link LEDs
- USB service port with current limiting
- LVDS display and USB/I2C capacitive touch
- RTC with backup source and documented drift target

### Power and environment

- 24 V nominal, 9–36 V continuous
- Reverse polarity and specified transient protection
- Closed enclosure, natural convection
- Peak workload at 50°C ambient without performance failure

### Software and lifecycle

- Reproducible Linux BSP
- Signed A/B updates with rollback
- Remote logs and local recovery mode
- Five-year availability target with PCN/EOL process
- Supplier delivers schematic, pin map, source, flashing tool, and board revision history

Now the team can compare RK3568 and RK3576 boards with evidence. RK3588 is not “better” unless the measured browser, vision, or future workload needs it.

## Supplier Evidence Package

Request the same package from every shortlisted supplier:

- Board datasheet and revision
- Dimensioned drawing and 3D model
- Schematic or sufficiently detailed design guide
- Connector part numbers and mating references
- SoC and memory orderable part numbers
- Interface multiplexing table
- Power and thermal test conditions
- Operating-temperature evidence
- BSP source, release notes, and known issues
- Compliance reports relevant to the board
- PCN/EOL and warranty process
- Sample lead time and production lead time
- Change-control policy for components and firmware

If one supplier answers with a polished brochure and another supplies auditable evidence, that difference belongs in the selection score.

## Validate With the Product, Not the Evaluation Kit

<figure style="margin:1.5rem 0;text-align:center">
  <img
    src="/images/posts/embedded-linux-build-system-hardware-photo.webp"
    alt="Real embedded board connected to a round display, adapter board, wireless module and cables"
    loading="lazy"
    decoding="async"
    style="max-width:100%;height:auto;border-radius:12px;box-shadow:0 6px 18px rgba(0,0,0,.08)">
  <figcaption style="color:#555;margin-top:.55rem">
    Original project photo: once a display, adapter, wireless link, USB service path, and application are connected, “the SBC” becomes a system. The specification must cover the complete operating combination.
  </figcaption>
</figure>

An open evaluation board at room temperature is appropriate for screening. Product approval requires:

- Production display and touch panel
- Final power input circuit and supply
- Target storage capacity and filesystem
- Intended radios, antennas, and cables
- Peak CPU/GPU/NPU workload
- Network outage and reconnection
- Repeated cold boot and brownout
- Interrupted update and recovery
- Final enclosure or thermally equivalent fixture
- Factory-flashed image

Use the [embedded SBC validation test plan](/posts/embedded-sbc-product-validation-checklist/) to turn each specification row into evidence.

## Common Specification Failures

### Copying a development board datasheet

This describes a candidate, not the product need. It can hide alternatives and preserve interfaces the product never uses.

### Using “support” without defining the layer

“Supports camera” may mean the SoC has CSI, the EVB schematic has a connector, or one vendor image contains one sensor driver. Specify the sensor, timing, driver, ISP path, and test.

### Ignoring simultaneous operation

Display, camera, AI, storage, and networking compete for memory and thermal budget. PCIe, SATA, USB, and display paths may share PHY resources.

### Treating temperature grade as thermal validation

A SoC or board component rating does not prove the closed product meets performance at ambient extremes.

### Leaving BSP ownership until procurement

Low board cost can be erased by an undocumented kernel fork, missing source, or unmaintained binary accelerator.

### Confusing prototype success with lifecycle readiness

The [SBC, SOM, and custom board decision](/posts/sbc-vs-som-vs-custom-board/) should include volume, change control, supply risk, and maintenance—not only prototype speed.

## Release Gate

Before approving a board for the next design phase, require:

- [ ] Every “must” requirement has a verification method.
- [ ] All interface conflicts and shared resources are resolved.
- [ ] Real workload benchmarks meet performance margin.
- [ ] Storage capacity and write endurance are calculated.
- [ ] Power peak, inrush, and peripheral budget are measured.
- [ ] Thermal performance is tested in a representative enclosure.
- [ ] BSP sources and a clean rebuild are verified.
- [ ] Update interruption and recovery pass.
- [ ] Factory flashing and interface test are demonstrated.
- [ ] Mechanical drawings and cable clearances are approved.
- [ ] Lifecycle, PCN, EOL, warranty, and change control are documented.
- [ ] Open risks have owners, dates, and exit criteria.

## Engineering Review Notes

This method intentionally separates requirements from candidate specifications and supplier claims. Quantitative examples are planning starting points, not universal standards. Replace them with measurements from the product workload and with limits from official component, interface, safety, EMC, and environmental documentation applicable to the target market.

The photographs are genuine hardware images from the site archive. They are used as physical integration examples; no electrical, thermal, or lifecycle claim is inferred from the images.

## FAQ

### What should an embedded SBC specification include?

It should cover workload, interfaces, compute margin, memory, storage, power, thermal conditions, mechanics, BSP deliverables, update and recovery, security, production test, lifecycle, supplier change control, and verification methods.

### Should the SoC model be specified in the PRD?

Usually not at first. The PRD should describe product outcomes and constraints. Add a specific SoC only when architecture, software compatibility, reuse, procurement, or validated performance makes it a real requirement.

### How much performance margin should an SBC have?

There is no universal percentage. A useful early guardrail is sustained CPU below roughly 70% in the defined peak scenario and 25–30% memory growth margin, but deterministic, safety-related, or burst workloads may require more.

### When is an SBC requirement testable?

It is testable when it defines the condition, measurable result, limits, environment, procedure or verification method, and pass/fail outcome. “Industrial temperature” is vague; a four-hour peak-workload test at a defined ambient temperature is testable.

<script type="application/ld+json">
{
  "@context":"https://schema.org",
  "@type":"FAQPage",
  "mainEntity":[
    {"@type":"Question","name":"What should an embedded SBC specification include?","acceptedAnswer":{"@type":"Answer","text":"It should cover workload, interfaces, compute margin, memory, storage, power, thermal conditions, mechanics, BSP deliverables, update and recovery, security, production test, lifecycle, supplier change control, and verification methods."}},
    {"@type":"Question","name":"Should the SoC model be specified in the PRD?","acceptedAnswer":{"@type":"Answer","text":"Usually not at first. The PRD should describe product outcomes and constraints. Add a specific SoC when architecture, software compatibility, reuse, procurement, or validated performance makes it a real requirement."}},
    {"@type":"Question","name":"How much performance margin should an SBC have?","acceptedAnswer":{"@type":"Answer","text":"There is no universal percentage. A useful early guardrail is sustained CPU below roughly 70 percent in the defined peak scenario and 25 to 30 percent memory growth margin, but some workloads require more."}},
    {"@type":"Question","name":"When is an SBC requirement testable?","acceptedAnswer":{"@type":"Answer","text":"It is testable when it defines the condition, measurable result, limits, environment, verification method, and pass or fail outcome."}}
  ]
}
</script>
