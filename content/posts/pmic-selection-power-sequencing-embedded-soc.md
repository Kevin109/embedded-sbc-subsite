---
title: "PMIC Selection and Power Sequencing for Embedded SoCs"
seo_title: "PMIC Selection and Power Sequencing for Embedded SoCs"
description: "Select an embedded SoC PMIC from a rail budget, validate regulator sequencing and reset, and build a repeatable brownout and cold-start test plan."
date: 2026-11-15T09:00:00+08:00
lastmod: 2026-11-24T09:00:00+08:00
draft: false
roadmap_id: "ESB-P030"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["PMIC selection","embedded SoC power sequencing","power rail budget","PGOOD reset timing","PMIC brownout validation"]
cover:
  image: "/images/posts/pmic-selection-power-sequencing-embedded-soc-hero.jpg"
  alt: "Power regulators, inductors and oscilloscope probes on an embedded PCB validation bench"
  caption: "AI-generated engineering illustration."
images: ["/images/posts/pmic-selection-power-sequencing-embedded-soc-hero.jpg"]
---

When a board starts from a bench supply and occasionally fails in the cabinet, look at the first milliseconds of power before changing the bootloader. A slowly rising supply, a cold regulator or an attached peripheral can put rails in a different order from the schematic's startup diagram. PMIC selection starts with the states the board must survive.

Build the power budget early in [custom SBC design](/custom-embedded-systems/). Use the processor's exact hardware design guidance and recommended operating limits. A reference board is a good origin for the design, provided its source supply, DDR type, loads, temperature and PMIC option match the conditions being carried over.

## Turn the reference diagram into a rail worksheet

| Field | What to record for each rail |
|---|---|
| Electrical window | Nominal voltage, operating tolerance, transient limits and measurement point |
| Loads | Steady, peak and startup current, plus simultaneous loads |
| Startup | Parent source, enable condition, ramp window, allowed ordering and PGOOD behavior |
| Operating modes | Performance states, suspend retention, consumers that can be switched off |
| Shutdown | Discharge behavior, allowed rail overlap, possible backfeed paths |
| Reset linkage | Which rails and clocks must be valid before reset can release |

Separate regulator capacity from system draw. A buck regulator delivering 1.00 V at 2.0 A supplies 2.0 W. At an illustrative 86% efficiency, it draws about 2.33 W upstream, or 0.47 A from an ideal 5 V source while in that steady state. Estimate input power across simultaneous loads and inspect regulator curves at realistic temperature and operating modes. Startup inrush, source tolerance, cable drop and capacitive charging need their own measurements.

Map the current step and the heating to physical components. Inductor saturation current, board copper, local capacitor derating and airflow all affect margin. Capture voltage at the processor-side test point during a representative workload step. If the board passes on a development heatsink, repeat with the intended mounting and cooling arrangement from the [fanless thermal design](/posts/fanless-industrial-embedded-computer-design/) review.

ST documents an example STM32MP25 power tree and sequencing arrangement using STPMIC25. Its datasheet also identifies nonvolatile defaults and ordering variants. These are examples of why the exact configured device and turn-on behavior belong in the BOM and factory acceptance plan. [STPMIC25 datasheet](https://www.st.com/resource/en/datasheet/stpmic25.pdf) · [ST STM32MP25 power design note](https://www.st.com/resource/en/application_note/an5727-how-to-use-stpmic25-to-develop-a-wall-adapter-powered-application-on-stm32mp25-mpus-stmicroelectronics.pdf).

{{< figure src="/images/posts/pmic-selection-power-sequencing-embedded-soc-workflow.svg" link="/images/posts/pmic-selection-power-sequencing-embedded-soc-workflow.svg" alt="Power validation flow from supply and rail ramp to power good, reset and fault recovery" caption="A typical dependency chain. Use the selected SoC and PMIC documentation for actual order and timing." >}}

## Evaluate integrated and discrete power designs

An integrated PMIC helps when one documented device can handle rail count, startup dependencies, standby modes and fault reporting. Inspect the combined dissipation, configuration process and availability of the specific ordering code. A PMIC with the right nominal channel voltages may fail the design review if its autonomous factory defaults release a rail before the processor permits it.

Discrete bucks, LDOs and a sequencer can make unusual loads easier to size and isolate heat. Count the routing, feedback, reset and monitoring circuitry needed to deliver equivalent behavior. Consider what must happen before an I²C controller exists. Software can tune rails after boot when supported, while the minimum path to release reset needs working hardware defaults. Use the [SBC schematic review](/posts/custom-sbc-schematic-review-checklist/) to compare both proposals at the pins rather than by converter count.

## Capture fault behavior deliberately

Set up the oscilloscope against a shared trigger and record input at the board, critical rails, relevant enable and power-good signals, and reset. Use short probe returns or appropriate differential measurement for the points concerned. Review rail crossings at the specified thresholds. A signal labeled PGOOD only tells you what the selected hardware and configuration monitor, so check masks, delays and the behavior when a rail is disabled. [TI integrated sequence example](https://www.ti.com/lit/ds/symlink/tps65216.pdf).

| Test | What makes the result useful |
|---|---|
| Cold start and slow input rise | Scope captures at the supply range and temperature corners defined for the product |
| Brownout and quick off/on | Repeatable trigger, known input profile, and a deterministic restart or safe hold |
| Peripheral attached before main power | Evidence that auxiliary paths do not backfeed unpowered I/O or rails |
| Suspend and resume | Verified retention, wake-source behavior and bounded recovery time |
| Load transition | Minimum rail voltage at the load and a valid reset or fault response if limits are exceeded |

Tie supply tests to the [SBC power input](/posts/embedded-sbc-power-input-design/) conditions and the [wide temperature validation](/posts/wide-temperature-validation-industrial-sbc/) plan. If a selection is shared across SKUs, choose stress cases that cover different memory and peripheral loads. Put the PMIC option, settings, schematic revision, reference waveforms and acceptable ranges in the release package. That gives manufacturing and field teams a concrete way to recognize a sequencing regression.
