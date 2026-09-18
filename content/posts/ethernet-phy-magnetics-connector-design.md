---
title: "Ethernet PHY, Magnetics, and Connector Design for SBCs"
seo_title: "Ethernet PHY, Magnetics, and RJ45 Design Guide for SBCs"
description: "Design reliable Ethernet on an SBC with PHY selection, RGMII timing, magnetics, RJ45 shielding, chassis grounding, PoE checks, ESD, layout, and validation."
date: 2026-09-15
lastmod: 2026-09-15
draft: false
roadmap_id: "ESB-P016"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["Ethernet PHY design", "Ethernet magnetics layout", "RJ45 connector design", "SBC Ethernet layout", "RGMII PCB design", "Ethernet ESD protection"]
cover:
  image: "/images/posts/ethernet-phy-magnetics-connector-design-hero.jpg"
  alt: "Macro engineering bench view of SBC Ethernet PHY, magnetics, RJ45 connector, probes, and cable tester"
images:
  - "/images/posts/ethernet-phy-magnetics-connector-design-hero.jpg"
---

Reliable Ethernet on a custom SBC depends on the complete path from the SoC MAC through the digital interface, PHY, termination, magnetics, connector, shield, chassis, cable, and software configuration. A reference schematic can supply the nominal circuit, but layout, strap timing, return paths, component substitutions, and enclosure grounding decide whether the port passes link, ESD, emissions, and temperature testing.

Start by defining speed, cable, environment, synchronization, power, isolation, and software needs. A 100BASE-TX service port and a dual-gigabit TSN gateway do not need the same architecture.

This guide extends our [embedded interface engineering hub](/embedded-interfaces/) and the earlier [industrial Ethernet interface planning workflow](/posts/rs485-can-ethernet-interface-planning/).

## Select the MAC-to-PHY Interface

Common interfaces include MII, RMII, GMII, RGMII, SGMII, and direct integrated PHYs.

| Interface | Typical use | Review focus |
|---|---|---|
| RMII | 10/100 Mb/s with fewer pins | 50 MHz reference clock architecture and timing |
| RGMII | 10/100/1000 Mb/s parallel link | Clock/data delay ownership and skew |
| SGMII | Serial MAC-to-PHY or switch link | SerDes support, reference clock, AC coupling |
| Integrated PHY | Lowest external part count | Package escape, analog supplies, approved magnetics |

For RGMII, determine whether transmit and receive delays are inserted by the MAC, PHY, or PCB. Do not enable internal delay in both ends. Record the device-tree or driver configuration with the schematic and timing budget.

## PHY Selection Checklist

- Required 10/100/1000 or multi-gigabit modes
- Industrial temperature and supply rails
- MAC interface compatibility and I/O voltage
- IEEE 1588 timestamping, TSN, EEE, cable diagnostics, or wake features
- Linux driver upstream status and bootloader support
- Reference clock input/output options
- Reset and strap behavior
- Qualified magnetics and layout guide
- Package and assembly capability
- Lifecycle and errata

Read strap pins as power-on analog circuits, not ordinary GPIO. LED loads, pull resistors, SoC leakage, and reset timing can change the sampled mode.

## Power and Clock Integrity

PHY analog rails can be sensitive to noise. Follow the datasheet's regulator, ferrite, decoupling, and sequencing recommendations. Place high-frequency capacitors close to supply pins with short return paths.

For the reference clock:

- Confirm frequency, accuracy, jitter, and voltage
- Define oscillator, crystal, or recovered/forwarded clock ownership
- Keep clock traces short and away from connector/common-mode current paths
- Check startup time relative to reset release
- Provide controlled probe access without a large stub

A link that works at room temperature may fail during cold oscillator startup or a short power interruption.

## Magnetics and RJ45

Ethernet magnetics provide isolation and common-mode behavior between PHY circuitry and cable. Use parts recommended or qualified for the PHY when possible. Check turns ratio, insertion loss, return loss, common-mode rejection, isolation rating, temperature, center-tap configuration, and PoE current if applicable.

An integrated-magnetics RJ45 can save space and reduce routing, but it may limit sourcing, temperature grade, LED options, or PoE arrangement. Discrete magnetics provide flexibility but require more placement and a clear isolation barrier.

Microchip's [official magnetics selection note](https://www.microchip.com/en-us/application-notes/an813-1) and [Gigabit Ethernet design guide](https://www.microchip.com/en-us/application-notes/an2054) illustrate why the magnetics must be treated as part of the PHY design rather than a generic transformer.

## MDI Routing and Isolation Barrier

TI's [Ethernet PHY PCB layout checklist](https://www.ti.com/lit/an/snla387/snla387.pdf) recommends controlled differential routing, short paths, nearby return vias where appropriate, and a keep-out under discrete magnetics.

Practical rules:

- Route each MDI pair as a controlled 100 Ω differential structure using the approved stack-up
- Keep the pair members coupled and minimize intra-pair skew
- Avoid plane splits and reference changes under the PHY-side routing
- Minimize vias; add an intentional return path when changing reference
- Keep transmit and receive pairs separated from clocks, switch nodes, and each other
- Place magnetics close to the connector according to the PHY guide
- Preserve the required copper/plane keep-out under the isolation component
- Do not route unrelated signals across the isolation barrier

Avoid copying universal length limits from another PHY. Use the selected vendor's layout guide and validate with the stack-up fabricator.

## Chassis, Shield, and ESD

The RJ45 shield should normally connect to chassis at the entry, not carry cable discharge through digital ground. The exact connection—direct, capacitive, RC, or enclosure-dependent—must be coordinated with safety and EMC design.

Place ESD protection where the vendor recommends and choose devices with suitable working voltage, capacitance, surge behavior, and package. Protection is not effective if the discharge route crosses the PHY before reaching chassis.

Review Bob Smith or common-mode termination against the reference circuit and PoE architecture. TI's [TLK1xx layout guide](https://www.ti.com/lit/an/slva531a/slva531a.pdf) explicitly notes that its shown Bob Smith arrangement does not apply unchanged to PoE designs.

## PoE Is Not a Connector Option

If the product supports PoE, define whether it is a powered device, power sourcing equipment, or pass-through system. The design affects magnetics center taps, bridge/controller, classification, isolation, inrush, thermal dissipation, surge, and safety spacing.

Never populate a non-PoE termination or center-tap network blindly around PoE magnetics. Review the selected PoE controller reference design and required standard/class with a qualified power engineer.

## Software and Diagnostics

Hardware validation needs software visibility:

- PHY ID and negotiated speed/duplex
- Link flap and symbol/error counters where supported
- Cable diagnostics
- EEE status
- RGMII delay or interface mode
- Interrupt versus polling behavior
- MAC address provisioning
- Suspend, resume, and wake behavior

Expose these in the [field diagnostics strategy](/posts/field-diagnostics-embedded-industrial-devices/) so a cable, magnetics, PHY, or network issue can be distinguished remotely.

The companion [Modbus RTU-to-TCP gateway design](/posts/modbus-rtu-tcp-industrial-gateway/) shows how Ethernet electrical reliability affects an actual industrial protocol product.

## Validation Plan

| Test | Conditions | Evidence |
|---|---|---|
| Link interoperability | Multiple switches, speeds, cable lengths | Negotiation and recovery logs |
| Throughput | Bidirectional TCP/UDP under CPU load | Sustained rate, loss, CPU, temperature |
| RGMII timing | Temperature/voltage corners | Timing margin or scope evidence |
| Cable faults | Open, short, pair swap where applicable | Safe behavior and diagnostics |
| ESD/EFT/surge | Product standard and final enclosure | No damage, bounded recovery |
| Emissions/immunity | Production cable and shield | Margin and failure mode |
| Temperature | Cold start to maximum ambient | Link and throughput stability |
| Power cycling | Short and long interruptions | Deterministic PHY reset and link return |
| Endurance | 72-hour traffic with other I/O | No link flap, leak, or thermal fault |

## Schematic and Layout Exit Checklist

- [ ] MAC interface, voltage, and timing ownership are documented
- [ ] PHY straps remain valid with LEDs and attached logic
- [ ] Clock and reset meet all startup corners
- [ ] Magnetics are qualified for the PHY, temperature, and PoE role
- [ ] Center taps and common-mode termination match the reference design
- [ ] MDI pairs have controlled impedance and continuous references
- [ ] Isolation keep-out and creepage are preserved
- [ ] RJ45 shield has an intentional chassis path
- [ ] ESD current reaches chassis without crossing sensitive circuitry
- [ ] Linux driver, device tree, diagnostics, and MAC provisioning are verified

## Engineering Sources and Review Notes

Layout and magnetics guidance was checked against TI's [Ethernet PHY PCB design checklist](https://www.ti.com/lit/an/snla387/snla387.pdf) and [TLK1xx design guide](https://www.ti.com/lit/an/slva531a/slva531a.pdf), plus Microchip's [Gigabit Ethernet design guide](https://www.microchip.com/en-us/application-notes/an2054). Exact termination, strap, delay, magnetics, and protection requirements are PHY-specific; the selected datasheet and reference design take precedence.

## FAQ

### Can any 1:1 Ethernet magnetics work with any PHY?

No. Check the PHY vendor's electrical requirements and qualified parts, including loss, common-mode rejection, center taps, isolation, temperature, and PoE current.

### Should there be a ground plane under Ethernet magnetics?

Vendor guidance commonly calls for a metal keep-out under discrete magnetics to limit coupling across the isolation barrier. Follow the exact PHY and magnetics layout guide; an integrated RJ45 may have different guidance.

### Why does Gigabit Ethernet work on some boards but fail EMC?

The differential data can function while pair imbalance, shield return, plane discontinuity, magnetics placement, or protection layout converts energy into common-mode emissions. Functional link testing is not EMC validation.
