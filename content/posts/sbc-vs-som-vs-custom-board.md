---
title: "SBC vs SOM vs Custom Board for Embedded Products"
seo_title: "SBC vs SOM vs Custom Board: How to Choose for Embedded Products"
description: "A practical comparison of SBCs, SOMs, and custom boards for embedded product development, covering schedule, cost, risk, lifecycle, firmware, testing, and production."
keywords: ["SBC vs SOM", "SOM vs custom board", "custom embedded board", "embedded product architecture", "single board computer vs module"]
date: 2026-01-12
draft: false
schema_type: "BlogPosting"
cover:
  image: "/images/posts/sbc-vs-som-vs-custom-board-hero.webp"
  alt: "SBC vs SOM vs Custom Board for Embedded Products hero image"
images:
  - "/images/posts/sbc-vs-som-vs-custom-board-hero.webp"
---

Choosing between a [standard SBC](/posts/sbc-overview/), a system-on-module, and a fully custom board is one of the first architecture decisions in an embedded product. It affects schedule, cost, firmware work, mechanical design, test coverage, supply risk, and how much control the team has over the final device. The wrong choice can make a product look fast during prototype work and slow during production.

This guide compares the three paths from a product engineering point of view. The goal is not to argue that one approach is always better. The right answer depends on volume, lifecycle, interface needs, enclosure constraints, software ownership, certification, and the team's ability to support hardware and BSP work.

## What a Standard SBC Solves

A standard SBC is the fastest way to begin software and system validation. It provides processor, memory, storage, power management, and common I/O on one board. For prototypes, pilot builds, internal tools, and low-volume systems, this can be the most practical path.

An SBC works well when:

- The enclosure can accept the board and connectors
- Required interfaces are already exposed
- The production volume is modest
- Time to prototype is more important than full optimization
- The supplier can provide stable board availability
- The software team wants a known starting point

The risk is that a general-purpose SBC may not match the final product cleanly. It may require cable adapters, unused connectors, awkward mounting, unprotected I/O, or a power input that does not fit the installation. These issues may be acceptable for low volume, but they can become expensive at scale.

## What a SOM Solves

A system-on-module moves the complex compute design onto a module and lets the product team design a [carrier board](/posts/compute-module-carrier-board-design/) around it. The module usually includes the SoC, memory, storage, power management, and high-speed layout work. The carrier board provides product-specific connectors, power input, protection, mounting, and interface routing.

A SOM works well when:

- The product needs custom I/O or mechanical fit
- The team wants to reduce high-speed design risk
- Volume is high enough to justify a carrier board
- The processor platform should remain stable across product variants
- Certification and wireless complexity should be contained
- The team needs better production control than a standard SBC provides

The carrier board is still real hardware. It needs schematic review, layout, signal integrity checks where appropriate, power validation, ESD protection, test points, and factory fixtures. A SOM reduces compute risk, but it does not eliminate system engineering.

## What a Fully Custom Board Solves

A fully custom board gives the most control. The team can optimize size, connector placement, power tree, thermal path, component cost, and production flow. This is attractive when volume is high, the enclosure is strict, or the product has unusual interfaces.

A custom board may be justified when:

- Standard boards and SOMs cannot meet mechanical requirements
- Bill of materials cost matters strongly at volume
- The product needs a highly integrated power or I/O design
- Long-term supply and revision control must be tightly managed
- The team has access to strong hardware and BSP engineering
- The product roadmap includes multiple variants built from the same design base

The tradeoff is risk. Memory layout, power sequencing, boot configuration, high-speed interfaces, thermal design, and low-level firmware all become the product team's responsibility. A custom board should not be chosen only because it looks cleaner. It should be chosen when the business and engineering case justify the added ownership.

## Compare Total Product Cost

Board price is only one part of cost. A low-cost SBC may require extra assembly labor, adapters, brackets, manual flashing, or field service. A SOM may cost more per unit but reduce engineering risk. A custom board may reduce unit cost but increase upfront development and validation.

Compare:

| Factor | SBC | SOM | Custom board |
|---|---|---|---|
| Prototype speed | Fast | Medium | Slow |
| Mechanical fit | Limited | Good | Best |
| Interface control | Limited | Good | Best |
| Upfront engineering | Low | Medium | High |
| Unit cost at volume | Variable | Medium | Best potential |
| BSP ownership | Supplier-led | Shared | Mostly internal |
| Production control | Limited | Good | Best |
| Risk level | Low early, higher later | Balanced | High early, lower if well executed |

The right choice is usually the one that minimizes total product risk, not just the one with the lowest hardware price.

## Firmware and BSP Impact

Software support should be part of the architecture decision. With a standard SBC, the supplier's BSP may already support most peripherals. With a SOM, the module BSP may be stable, but the carrier board still needs device tree changes and interface validation. With a custom board, the [BSP must match the exact hardware design](/posts/embedded-bsp-bring-up-checklist/).

Ask:

- Who maintains bootloader and kernel changes?
- Can the image be rebuilt later?
- How are board revisions tracked?
- What happens when a display, PHY, or wireless module changes?
- How are updates and recovery handled?
- Can factory flashing be automated?

If the team does not have BSP capacity, a full custom board may create hidden schedule risk.

## Decision Guidance

Use an SBC when speed, low initial risk, and modest volume matter most. Use a SOM when the product needs custom I/O or mechanical fit but the team wants to avoid raw SoC design risk. Use a custom board when volume, size, cost, lifecycle, or integration requirements justify full ownership.

Many successful products move through these stages: SBC for proof of concept, SOM carrier for early production, custom board for mature volume. The path does not need to be decided forever on day one, but the team should avoid prototype choices that block later migration.

## FAQ

### Is an SBC enough for a commercial embedded product?

Sometimes. An SBC can be enough for low-volume or fast-deployment products if it fits the enclosure, interfaces, lifecycle, power, and support requirements.

### When is a SOM better than an SBC?

A SOM is better when the product needs custom connectors, protected I/O, improved mechanical fit, or production control while avoiding the full risk of raw SoC board design.

### When should a team design a fully custom board?

A custom board makes sense when volume, enclosure fit, cost optimization, special interfaces, lifecycle control, or product integration justify the extra engineering and validation effort.

<script type="application/ld+json">
{
  "@context":"https://schema.org",
  "@type":"FAQPage",
  "mainEntity":[
    {"@type":"Question","name":"Is an SBC enough for a commercial embedded product?","acceptedAnswer":{"@type":"Answer","text":"Sometimes. An SBC can be enough for low-volume or fast-deployment products if it fits the enclosure, interfaces, lifecycle, power, and support requirements."}},
    {"@type":"Question","name":"When is a SOM better than an SBC?","acceptedAnswer":{"@type":"Answer","text":"A SOM is better when the product needs custom connectors, protected I/O, improved mechanical fit, or production control while avoiding the full risk of raw SoC board design."}},
    {"@type":"Question","name":"When should a team design a fully custom board?","acceptedAnswer":{"@type":"Answer","text":"A custom board makes sense when volume, enclosure fit, cost optimization, special interfaces, lifecycle control, or product integration justify the extra engineering and validation effort."}}
  ]
}
</script>
