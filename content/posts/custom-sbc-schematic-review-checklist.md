---
title: "Custom SBC Schematic Review Checklist"
seo_title: "Custom SBC Schematic Review Checklist for First-Pass Hardware"
description: "Review a custom SBC schematic systematically across power, reset, clocks, boot, DDR, storage, Ethernet, USB, display, debug, protection, and production test."
date: 2026-07-24
lastmod: 2026-07-24
draft: false
roadmap_id: "ESB-P007"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["SBC schematic review checklist", "custom SBC design review", "embedded board schematic checklist", "hardware schematic review", "SBC power design", "PCB design review"]
cover:
  image: "/images/posts/custom-sbc-schematic-review-checklist-hero.jpg"
  alt: "Custom SBC schematic review desk with printed circuits, development board, oscilloscope, meter, and calipers"
images:
  - "/images/posts/custom-sbc-schematic-review-checklist-hero.jpg"
---

A custom SBC schematic review should find interface assumptions before they become PCB traces. The review is not a meeting where the designer scrolls through every page and asks whether anyone sees a problem. It is a controlled check against requirements, silicon documentation, reference circuits, power states, layout constraints, production test, and known failure modes.

The most effective review produces evidence: completed checklists, marked-up PDFs, rail and clock tables, pin-mux reports, unresolved-action owners, and a signed release baseline. It should happen before layout begins and again before PCB release.

This checklist belongs within the broader [custom embedded systems engineering process](/custom-embedded-systems/) and should follow a clear [compute-module or carrier-board partition decision](/posts/compute-module-carrier-board-design/).

## Prepare the Review Package

Do not start until reviewers have:

- Product requirements and interface matrix
- SoC, PMIC, DDR, storage, PHY, connector, and protection datasheets
- Latest silicon errata and hardware design guide
- Reference design schematic and bill of materials
- Power-tree diagram with load estimates
- Clock and reset tree
- Boot-mode and pin-mux tables
- Interface voltage-domain table
- PCB stack-up and preliminary placement constraints
- Manufacturing, factory-test, and service requirements
- Open questions with named owners

Review component ordering codes, not family names. Temperature grade, package, memory density, PHY option, and boot ROM support can change with the exact suffix.

## 1. Power Tree and Sequencing

For every rail, record source, nominal voltage, tolerance, maximum load, startup order, discharge behavior, always-on state, sleep state, and measurement point.

- [ ] Input range includes tolerance, cable drop, surge, and brownout
- [ ] Reverse polarity, overcurrent, inrush, and transient protection are defined
- [ ] PMIC rails match the selected SoC and memory grade
- [ ] Regulator current includes transient and growth margin
- [ ] Decoupling values, dielectric, package, and placement constraints are captured
- [ ] Power-good and enable logic cannot create an illegal partial-power state
- [ ] Back-power paths through GPIO, USB, HDMI, UART, or expansion headers are controlled
- [ ] Discharge and restart behavior is defined after a short interruption
- [ ] Test points exist for critical rails without creating large stubs

The [embedded SBC power-input reliability guide](/posts/embedded-sbc-power-input-design/) covers source transients, protection, and startup testing. During review, simulate or calculate the worst startup combination rather than summing typical loads.

## 2. Reset, Watchdog, and Boot Modes

- [ ] Power-on reset meets minimum pulse width and rail-stability requirements
- [ ] External reset cannot violate PMIC or SoC sequencing
- [ ] Watchdog reset reaches every state that must recover
- [ ] Boot straps have defined pulls and are not overridden by attached peripherals
- [ ] Strap sampling time is checked against RC values and external drive
- [ ] Recovery boot is accessible in the enclosure or factory fixture
- [ ] Boot-media selection and fallback are documented
- [ ] Reset cause is available to software and diagnostics

Do not reuse a boot-strap pin casually. A display, level shifter, or test fixture can drive the pin during the sampling window and produce an intermittent “wrong boot source” failure.

## 3. Clocks and Oscillators

Create a clock table with frequency, accuracy, jitter requirement, load capacitance, voltage, startup time, consumer, and layout rule.

- [ ] Crystal or oscillator matches the silicon vendor's approved range
- [ ] Load capacitors include pin and PCB parasitics
- [ ] RTC source and backup supply behavior are defined
- [ ] Ethernet, USB, PCIe, audio, camera, and display reference-clock requirements are met
- [ ] Spread-spectrum choices are compatible with every consumer
- [ ] Unused clock inputs are terminated as specified
- [ ] Clock observation or debug method exists where practical

Use the vendor hardware guide and silicon errata as the authority. Reference schematics demonstrate one configuration; they do not replace the datasheet.

## 4. DDR and High-Speed Memory

DDR review must connect schematic choices to placement and stack-up.

- [ ] Exact memory device is supported by controller, boot firmware, and training code
- [ ] Density, ranks, width, banks, and address map are correct
- [ ] Voltage rails, VREF, VTT, ZQ, and termination follow the selected topology
- [ ] Byte lanes and polarity swaps are legal and documented
- [ ] Package escape and trace topology are feasible on the planned layer count
- [ ] Length, impedance, via, reference-plane, and skew constraints are in layout rules
- [ ] Power sequencing and self-refresh states are reviewed
- [ ] DDR test software and margin-test plan are available

A green boot log is not a DDR qualification. Plan stress patterns, temperature testing, voltage corners, and multiple board samples.

## 5. Boot Storage and Non-volatile Data

- [ ] Boot ROM supports the exact eMMC, SD, SPI NOR, SPI NAND, or UFS arrangement
- [ ] Pull-ups, bus width, voltage switching, reset, and boot partitions are correct
- [ ] Storage capacity includes A/B images, update staging, logs, and future growth
- [ ] Write-protect and secure-storage functions have defined ownership
- [ ] Factory identity and calibration data have a protected location and backup policy
- [ ] Recovery remains possible after an interrupted update

Apply the [embedded storage reliability checklist](/posts/embedded-sbc-storage-reliability/) before choosing the part purely on capacity and price.

## 6. Interfaces and Voltage Domains

Review each connector from the outside inward: pinout, mating sequence, protection, filtering, common-mode path, PHY, level shifter, and SoC pin.

| Interface | Critical schematic questions |
|---|---|
| Ethernet | Correct PHY straps, clock, MDIO pulls, magnetics, center taps, chassis/ESD path? |
| USB | Role, VBUS source/sink, current limit, CC logic, ESD capacitance, controlled impedance? |
| PCIe | Refclk architecture, reset timing, lane map, AC coupling, sideband voltage? |
| MIPI/HDMI/eDP/LVDS | Lane count, voltage rails, AUX/DDC, hot plug, ESD, panel power sequence? |
| Camera | MCLK, I²C voltage, reset/power-down, supplies, connector pinout? |
| UART/RS485/CAN | Logic level vs field level, isolation, termination, bias, protection? |
| GPIO | Default state, voltage tolerance, pull direction, external drive during power-off? |

Cross-check the approved pin-mux output against every sheet. A net name can look correct while the SoC ball is assigned to a different alternate function.

## 7. Debug, Test, and Manufacturing

- [ ] Boot console is accessible and voltage labeled
- [ ] JTAG/SWD has a controlled production policy
- [ ] Critical rails, reset, clocks, and buses have probe access
- [ ] Factory programming works on a blank device
- [ ] Boundary scan or bed-of-nails strategy is feasible
- [ ] Test points fit fixture probe size and spacing
- [ ] Unique identity, MAC addresses, and keys have a provisioning path
- [ ] Debug interfaces can be locked without removing recovery capability

Coordinate these decisions with the [factory test fixture design process](/posts/factory-test-fixture-design-embedded-products/). Test access added after layout usually creates stubs, crowding, or manual operations.

## 8. Protection, Safety, and EMC

- [ ] Every external signal has an ESD and surge classification
- [ ] Protection voltage and capacitance suit the interface
- [ ] Return path reaches chassis or ground without crossing sensitive circuitry
- [ ] Isolation voltage, creepage, and clearance match the working environment
- [ ] Connector shield termination is explicit
- [ ] Inductive loads have a controlled flyback path
- [ ] User-accessible power is current limited
- [ ] Thermal shutdown and fault behavior are safe

Use the [embedded EMC and ESD review checklist](/posts/emc-esd-design-checklist-embedded-systems/) to translate the installation environment into circuit and layout actions.

## 9. Schematic-to-Layout Handoff

Annotate requirements that the schematic alone cannot prove:

- Placement order and maximum distance
- Differential impedance and pair skew
- Intra-group DDR matching
- Reference planes and forbidden splits
- Keep-outs below magnetics, antennas, crystals, and isolation barriers
- Sensitive analog return paths
- Thermal vias and copper area
- ESD device order from connector to protected IC
- Test-point access and connector keep-out

The follow-on [embedded board DFM checklist](/posts/design-for-manufacturing-embedded-boards/) should verify that electrical intent survives panelization, assembly, inspection, and factory test.

## Review Exit Criteria

Do not release the schematic because the meeting ended. Release it when:

1. Every checklist item is pass, not applicable with rationale, or an owned action.
2. All component variants and alternates are reviewed.
3. Power, clock, reset, boot, pin-mux, and interface tables match the schematic revision.
4. Reference-design deviations are documented.
5. Layout rules are imported or formally handed off.
6. Manufacturing and firmware reviewers accept test, provisioning, and recovery access.
7. A PDF, source archive, BOM, and review record are stored under revision control.

## Engineering Sources and Review Notes

The checklist structure follows common silicon-vendor hardware design guides and focused official checklists such as Microchip's [schematic review checklist for timing devices](https://www.microchip.com/en-us/application-notes/an3836) and TI's [Ethernet PHY PCB design checklist](https://www.ti.com/lit/an/snla387/snla387.pdf). Exact values, sequencing, topology, and layout constraints must come from the datasheet and hardware guide for the selected ordering codes. A generic checklist must never override device-specific documentation.

## FAQ

### Who should attend a custom SBC schematic review?

At minimum: the hardware designer, a second hardware reviewer, PCB layout engineer, BSP/firmware engineer, mechanical or thermal owner, and manufacturing/test engineer. Security and compliance owners should join when their requirements affect hardware.

### Should layout start before schematic review is complete?

Preliminary placement can expose feasibility issues, but routing high-risk interfaces before power, pin-mux, package, and stack-up decisions are closed creates avoidable rework.

### Is a vendor reference schematic enough?

No. It represents one board, memory device, PMIC configuration, interface set, and product assumption. Review every deviation and every product-specific connector, protection, and power state.
