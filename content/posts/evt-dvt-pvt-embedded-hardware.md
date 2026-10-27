---
title: "EVT, DVT, and PVT for Embedded Hardware Products"
seo_title: "EVT vs DVT vs PVT: Embedded Hardware Validation Gates"
description: "Define EVT, DVT, and PVT exit criteria for embedded hardware, including sample selection, defect closure, production yield, and firmware traceability."
date: 2026-09-23
lastmod: 2026-10-27
draft: false
roadmap_id: "ESB-P018"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["EVT DVT PVT", "embedded hardware validation", "hardware development stages", "production validation testing", "embedded product NPI"]
cover:
  image: "/images/posts/evt-dvt-pvt-embedded-hardware-hero.jpg"
  alt: "Illustrative electronics bench showing a reworked prototype, production-intent board, and finished controller"
  caption: "AI-generated engineering illustration of development stages; not a customer production line."
images: ["/images/posts/evt-dvt-pvt-embedded-hardware-hero.jpg"]
---

EVT, DVT, and PVT are useful only when each build retires a different kind of risk. Making three batches of the same board and assigning a new acronym to each does not establish production readiness.

For an embedded product, the central questions are straightforward. Does the architecture work? Does the intended design meet its requirements? Can the manufacturing process repeatedly deliver that design with adequate evidence? Firmware, enclosure, provisioning, and test fixtures belong in those questions from the start.

This guide provides a stage-gate framework for [custom embedded product development](/custom-embedded-systems/). It is an engineering planning guide, not a claim that a particular number of samples or named phase guarantees certification.

## Define the Gates Before Ordering Boards

[Instrumental's stage-gate definitions](https://instrumental.com/build-better-handbook/evt-dvt-pvt) describe EVT, DVT, and PVT as distinct development and manufacturing milestones. Organizations use the terms somewhat differently. Put your own entry and exit criteria in the project plan so suppliers and internal teams are working toward the same evidence.

| Stage | Main question | Representative exit evidence |
|---|---|---|
| EVT: engineering validation | Does the chosen architecture work with acceptable margin? | Bring-up results, interface measurements, unresolved-risk list |
| DVT: design validation | Does the production-intent configuration satisfy the specification? | Requirements traceability, environmental and functional reports, closed design defects |
| PVT: production validation | Can the intended process build and verify the product repeatedly? | Station capability, yield analysis, traceability, controlled work instructions |

The gate is not a date on a slide. Give every requirement an owner, a verification method, a configuration identifier, and a pass criterion. Our [embedded requirements specification checklist](/posts/embedded-product-requirements-specification/) is a useful starting point when the product brief still says things such as “fast boot” or “industrial reliability.”

{{< figure src="/images/posts/evt-dvt-pvt-embedded-hardware-workflow.svg" link="/images/posts/evt-dvt-pvt-embedded-hardware-workflow.svg" alt="EVT architecture evidence flows to DVT design evidence and PVT process evidence, with changes requiring impact review" caption="Each gate answers a different question. A later design change can invalidate earlier evidence." >}}

## EVT: Make the Risk Visible

An EVT board may have rework wires, debug headers, and a temporary heatsink. Those accommodations are acceptable when documented; they are dangerous when forgotten. A successful boot with a bench supply does not prove that the intended input protection or production power sequence works.

Build a risk-ranked test list. Prioritize power rails, reset sequencing, memory stability, boot media, high-speed interfaces, and the most uncertain workload. Measure enough to distinguish a design margin from a lucky unit. Record actual component substitutions, board revisions, firmware hashes, and instruments used.

An EVT exit packet should explain every workaround. For example, a system that meets its throughput target only with a laboratory fan has not closed the thermal architecture risk. Either demonstrate a feasible enclosure solution or carry the issue forward as an explicit blocker with an owner and resolution date.

Use the [embedded BSP bring-up procedure](/posts/embedded-bsp-bring-up-checklist/) to align software and hardware evidence. A driver workaround that hides an electrical problem should not quietly become the permanent solution.

## DVT: Validate the Configuration You Intend to Sell

DVT should move toward production-intent PCB, BOM, enclosure, connectors, storage, firmware, and cooling. Where an element is not final, identify exactly which results need repeating after it changes.

The useful deliverable is a requirements-to-evidence matrix:

| Requirement | Configuration and condition | Evidence | Disposition |
|---|---|---|---|
| Boots at minimum input voltage | Final input path, cold start, intended peripherals | Supply trace and readiness timing | Pass or tracked defect |
| Maintains inspection throughput | Closed enclosure, worst permitted workload | Frame drops, latency, component temperatures | Pass or tracked defect |
| Recovers from interrupted update | Released partition layout and signing policy | Repeated interruption log | Pass or tracked defect |
| Connectors tolerate installation | Production cable and mounting arrangement | Inspection and functional results | Pass or tracked defect |

Environmental and compliance testing must follow the applicable product requirements and agreed laboratory methods. A chamber run is not automatically a formal compliance result. Plan [industrial SBC temperature validation](/posts/wide-temperature-validation-industrial-sbc/) around the assembled product, not only the processor temperature rating.

Keep exploratory stress testing separate from acceptance testing. Both are valuable, but a deliberately destructive margin experiment should not be confused with the specified operating test. Record which units were exposed to which stresses before reusing them.

## PVT: Validate the Process, Including Its Failure Paths

PVT is where operator actions, fixture tolerances, software versions, component handling, and station permissions become part of the product story. Use the intended manufacturing sequence and collect station-level results tied to each unit.

Track first-pass yield separately from final yield. A line that eventually ships every unit after repeated retesting may conceal unstable contacts or an intermittent assembly defect. Preserve the first result, retest reason, repair action, and final disposition. Do not overwrite a failure with the latest pass.

Challenge the test system with controlled known-good and known-fault units. Confirm that wrong firmware, missing peripherals, duplicate identities, and incomplete calibration are rejected where relevant. Avoid creating hazardous faults just to demonstrate detection; define safe fault-injection methods with the hardware team.

The [factory test fixture design process](/posts/factory-test-fixture-design-embedded-products/) should cover contact wear, maintenance intervals, and station correlation. For connected products, incorporate [secure factory identity provisioning](/posts/secure-device-identity-factory-provisioning/) into the gate: an electrically good board with an unusable certificate is not a shippable unit.

## Choose Samples Based on the Claim

There is no universal rule that EVT needs ten units or PVT needs one hundred. Select samples to cover the risks: component lots, PCB panels, assembly lines, operators, fixtures, configuration variants, and environmental corners. A small engineering sample may reveal a mechanism without establishing its production frequency.

For an illustrative statistical check, assume independent units with the same unknown defect probability. With zero failures in `n` trials, a one-sided 95% upper confidence bound is `1 − 0.05^(1/n)`. Thirty passing units still allow an upper bound of about 9.5%; one hundred reduce it to about 3.0%. These are calculations, not measured results or recommended release limits. The underlying model is the [binomial distribution documented by NIST](https://itl.nist.gov/div898/handbook/eda/section3/eda366i.htm).

Repeatedly testing one board does not produce one hundred independent manufacturing samples. Common-lot defects and shared process conditions also weaken the simple model. Agree on the reliability claim and sampling plan with the responsible quality engineer before using a confidence calculation in a release decision.

## Handle Changes Without Losing the Evidence

After a gate, every material change needs an impact review. A new storage device can affect boot timing, power interruption behavior, and temperature performance. A resistor substitution may be harmless in one circuit and significant in another. The review should name the affected requirements and the regression tests required, not merely label the change “equivalent.”

Maintain a release ledger containing hardware revision, BOM revision, firmware and test-software hashes, fixture revision, approved deviations, and signed gate decisions. Temporary deviations need an expiry or closure condition; otherwise they become undocumented product variants.

### Can EVT and DVT overlap?

Yes, when the risks and dependencies are explicit. Testing enclosure fit while firmware is still being completed can be productive. Calling the entire design validated before the unfinished firmware-dependent tests pass is not.

### What should block production release?

Unresolved safety or security issues, missing requirement evidence, uncontrolled configuration changes, and a process that cannot reliably detect known critical faults are strong blockers. Commercial urgency does not turn missing evidence into a pass.
