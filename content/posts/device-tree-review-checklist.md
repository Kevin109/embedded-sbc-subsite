---
title: "Device Tree Review Checklist for Embedded Linux Boards"
seo_title: "Device Tree Review Checklist for Embedded Linux Boards"
description: "A practical device tree review checklist for embedded Linux boards, covering pinmux, regulators, clocks, GPIOs, Ethernet, display, touch, USB, storage, and board revisions."
keywords: ["device tree checklist", "embedded Linux device tree", "Linux BSP review", "device tree pinmux", "embedded board bring-up"]
date: 2026-04-07
draft: false
schema_type: "BlogPosting"
cover:
  image: "/images/posts/device-tree-review-checklist-hero.webp"
  alt: "Device Tree Review Checklist for Embedded Linux Boards hero image"
images:
  - "/images/posts/device-tree-review-checklist-hero.webp"
---

Device tree is one of the most important files in an [embedded Linux BSP](/posts/embedded-bsp-bring-up-checklist/). It describes how the board hardware is connected so the kernel can initialize devices correctly. A small mistake can cause missing Ethernet, unstable display, broken touch, wrong GPIO defaults, power sequencing issues, or failures that appear only after a board revision.

This checklist is written for engineers reviewing device tree files for SBCs, SOM carrier boards, and custom embedded boards. It applies to many SoC platforms, including NXP i.MX, ST STM32MP, TI, Qualcomm, MediaTek, and other Linux-capable processors.

## Start With the Exact Board Revision

Device tree must match hardware. Before reviewing nodes and properties, confirm the board revision, module revision, BOM options, and product variant. If a carrier board has multiple configurations, the BSP should make that explicit.

Record:

- Board name and revision
- SoC or module variant
- Memory size
- Storage option
- Ethernet PHY part
- Display panel and touch controller
- Wi-Fi or cellular module
- Power tree differences
- Optional interfaces

Many device tree problems come from reusing an old file without updating it for the actual hardware.

## Pinmux and GPIO Defaults

Pin multiplexing controls which function appears on each SoC pin. It is easy to create conflicts, especially when one board exposes many interfaces.

Review:

- Required interfaces enabled
- Unused pins set to safe states
- Pull-ups and pull-downs match schematic intent
- GPIO default states during boot
- Reset lines active level
- Interrupt polarity
- Shared pins and alternate functions
- Boot-critical pins not repurposed incorrectly

GPIO defaults matter because peripherals see pins before the application starts. A wrong default can enable a power rail too early, hold a transceiver in the wrong mode, or toggle an output during boot.

## Regulators, Clocks, and Resets

Power sequencing is often described through regulators, enable GPIOs, clocks, and reset lines. If these are wrong, devices may appear intermittent.

Check:

- Regulator voltage values
- Always-on rails
- Enable GPIO polarity
- Startup delays
- Power dependencies between devices
- Clock source and frequency
- Reset timing
- Suspend and resume behavior

The device tree should reflect the real power tree. If a device depends on a rail, that dependency should be represented so the kernel can manage initialization and power states correctly.

## Ethernet and Networking

Ethernet failures are common during board bring-up because the SoC, MAC interface mode, PHY address, reset timing, and clocking must all match.

Review:

- PHY interface mode: RGMII, RMII, SGMII, or other
- PHY address
- Reset GPIO and delay
- Reference clock direction
- MAC address handling
- Pinmux drive strength where relevant
- EEE or power-saving settings if they cause issues
- Dual Ethernet configuration, if present

If the board uses an external switch, cellular modem, or multiple network interfaces, the device tree should align with the intended software architecture.

## Display and Touch

[Display bring-up](/posts/display-touch-interface-integration/) depends on panel timing, interface selection, power sequencing, backlight control, and sometimes bridge chips. Touch depends on I2C or USB, interrupt lines, reset lines, and coordinate orientation.

Review:

- Panel compatible string
- Resolution and timing
- DSI, LVDS, RGB, HDMI, or bridge configuration
- Backlight node and PWM
- Panel power rails
- Reset and enable GPIOs
- Touch controller address
- Touch interrupt and reset GPIO
- Rotation and coordinate mapping where needed

Display and touch should be tested together after review. A correct-looking display can still have touch mapping or resume problems.

## USB, PCIe, and Storage

High-speed interfaces require both hardware and firmware alignment. Device tree should describe host/device roles, power switching, resets, regulators, and lane configuration.

Check:

- USB host or device role
- VBUS power control
- Overcurrent GPIOs
- PCIe reset and clock
- Lane mapping
- SD card detect and write protect
- eMMC timing mode
- NVMe or SATA power rails
- Wake behavior if used

Storage configuration deserves extra care because it affects boot reliability and [update strategy](/posts/secure-firmware-update-rollback/). If the product uses A/B updates or recovery partitions, the bootloader and Linux view of storage should be consistent.

## Production and Maintainability

Device tree should be maintainable. Product teams should avoid unexplained copied nodes, dead configurations, and manual edits that are not tied to hardware revisions.

Good practice includes:

- Comments only where they explain board-specific decisions
- Clear naming for product variants
- Version control tied to hardware revisions
- Review against schematic
- Test notes for critical peripherals
- Release notes listing device tree changes

A device tree review is not just a software task. Hardware, firmware, test, and manufacturing teams should all understand the board-specific assumptions.

## FAQ

### Why is device tree important in embedded Linux?

Device tree tells the Linux kernel how board hardware is connected. It affects pinmux, regulators, clocks, buses, displays, Ethernet, USB, storage, and many peripherals.

### Who should review device tree files?

Firmware engineers should review them with hardware engineers, because device tree must match the schematic, power tree, connector design, and board revision.

### What is a common device tree mistake?

A common mistake is copying a reference device tree and leaving pinmux, regulator, PHY, display, or GPIO settings that do not match the actual board.

<script type="application/ld+json">
{
  "@context":"https://schema.org",
  "@type":"FAQPage",
  "mainEntity":[
    {"@type":"Question","name":"Why is device tree important in embedded Linux?","acceptedAnswer":{"@type":"Answer","text":"Device tree tells the Linux kernel how board hardware is connected. It affects pinmux, regulators, clocks, buses, displays, Ethernet, USB, storage, and many peripherals."}},
    {"@type":"Question","name":"Who should review device tree files?","acceptedAnswer":{"@type":"Answer","text":"Firmware engineers should review them with hardware engineers, because device tree must match the schematic, power tree, connector design, and board revision."}},
    {"@type":"Question","name":"What is a common device tree mistake?","acceptedAnswer":{"@type":"Answer","text":"A common mistake is copying a reference device tree and leaving pinmux, regulator, PHY, display, or GPIO settings that do not match the actual board."}}
  ]
}
</script>
