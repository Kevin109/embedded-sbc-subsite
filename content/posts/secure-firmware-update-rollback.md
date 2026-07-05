---
title: "Secure Firmware Update and Rollback for Embedded Products"
seo_title: "Secure Firmware Update and Rollback for Embedded Products"
description: "A practical guide to secure firmware updates and rollback for embedded products, covering signed images, A/B updates, recovery partitions, configuration migration, diagnostics, and factory release control."
keywords: ["secure firmware update", "embedded OTA update", "firmware rollback", "A/B update embedded Linux", "embedded firmware security"]
date: 2026-03-20
draft: false
schema_type: "BlogPosting"
cover:
  image: "/images/posts/secure-firmware-update-rollback-hero.webp"
  alt: "Secure Firmware Update and Rollback for Embedded Products hero image"
images:
  - "/images/posts/secure-firmware-update-rollback-hero.webp"
---

Secure firmware update is a product requirement, not an optional feature added after release. Embedded devices may run for years in factories, retail sites, vehicles, buildings, laboratories, or customer networks. They need security fixes, application updates, configuration changes, and sometimes emergency recovery. If the update process is unreliable, every field update becomes a business risk.

This guide explains how to design firmware update and rollback for [embedded Linux products](/posts/industrial-linux/), SBC-based devices, custom carrier boards, gateways, HMI terminals, and industrial computers. The exact implementation may use A/B partitions, a recovery image, a package manager, or a vendor tool, but the engineering principles are similar.

## Define the Update Risk

Different products need different update strategies. A device on a service bench can be recovered manually. A gateway installed in a remote cabinet cannot. A medical or industrial device may need strict release control. A consumer-facing terminal may need silent updates with minimal downtime.

Start by defining:

- Can the device be physically accessed after deployment?
- What happens if power fails during update?
- Is network connectivity reliable?
- How large are firmware images?
- Is user data stored locally?
- Are security patches expected during the product life?
- Who approves and signs releases?
- How does support confirm which version is installed?

This risk model decides whether simple updates are acceptable or whether rollback and recovery are mandatory.

## Signed Images and Trust

A secure update process should verify authenticity before installing firmware. Signed images help ensure that only approved releases are accepted. This protects against accidental wrong images and deliberate tampering.

Important design choices include:

- Where signing keys are stored
- How public keys are provisioned into the device
- Whether bootloader verifies the image
- Whether the root filesystem or application is also verified
- How key rotation is handled
- How debug or service images are controlled

Secure boot and signed updates are related but not identical. Secure boot verifies what runs at boot. Signed update verifies what is accepted during update. Product security is stronger when both are designed together.

## A/B Update Design

A/B updates use two firmware slots. The device runs from one slot while the new image is written to the other. After installation, the bootloader tries the new slot. If boot succeeds and the application confirms health, the new slot becomes active. If boot fails, the device can roll back.

A/B updates are useful when field access is limited, but they require enough storage and careful boot logic.

Design details include:

- Slot layout
- Boot attempt counter
- Health check criteria
- Rollback trigger
- Shared data partition
- Configuration migration
- Logs from failed boots
- Factory flashing of both slots or one known-good slot

The health check should verify more than kernel boot. The application, critical services, network, and required hardware may need to start correctly before the update is marked good.

## Recovery Partition or Service Mode

Some products use a recovery partition instead of full A/B updates. A minimal recovery system can reinstall firmware, expose a service interface, or restore a known-good image. This approach may use less storage, but recovery behavior must be reliable and documented.

Recovery mode should answer:

- How is it entered?
- Can it be triggered remotely?
- Can it recover after corrupted root filesystem?
- Is the recovery image protected from normal writes?
- Does it require user action?
- How are logs preserved?

For industrial products, service mode should be accessible enough for support but controlled enough to prevent misuse.

## Configuration and Data Migration

Firmware updates often fail not because the image is bad, but because configuration migration is weak. New software may expect new settings, database schema changes, certificate formats, or calibration data.

Plan:

- Versioned configuration files
- Backups before migration
- Forward migration steps
- Rollback behavior after migration
- Separation of firmware and user data
- Factory defaults
- Validation after update

If rollback is possible, configuration compatibility must be considered. Rolling back software while leaving upgraded configuration can break the system. Some products prevent rollback after certain migrations; others maintain backward-compatible configuration formats.

## Diagnostics and Release Control

Support teams need clear update diagnostics. The device should report current version, previous version, update attempt time, failure reason, boot slot, rollback status, and relevant logs. Without this, field troubleshooting becomes guesswork.

Release control should include:

- Version naming convention
- Release notes
- Supported hardware revisions
- Known issues
- Test results
- Signing record
- Rollout plan
- Rollback plan

For products using NXP i.MX, ST STM32MP, Qualcomm, MediaTek, TI, or other SoC platforms, release notes should also include bootloader, kernel, [device tree](/posts/device-tree-review-checklist/), and root filesystem versions. Firmware is not just the application.

## Factory and First Boot

The factory process should install firmware in a state compatible with the update design. It may need to program keys, serial numbers, MAC addresses, device identity, and initial configuration. First boot should verify that the device can report its version and update status.

Factory mistakes can break update security. For example, duplicated credentials, missing public keys, wrong hardware revision metadata, or debug-enabled images can create field problems later.

## Practical Recommendation

For low-risk local devices, a signed image plus recovery mode may be enough. For remote gateways, industrial controllers, and customer-network devices, A/B update with rollback is usually worth serious consideration. For any product, the update process should be tested by interrupting power, corrupting downloads, using wrong images, and forcing service recovery.

An update system is only trustworthy after it fails safely.

## FAQ

### What is firmware rollback?

Firmware rollback is the ability to return to a previous working firmware image when a new update fails to boot, fails health checks, or creates serious operational problems.

### Are signed firmware images enough for secure updates?

Signed images are important, but they are not enough alone. A secure update design also needs key management, rollback or recovery, debug control, release process, and diagnostics.

### When should embedded products use A/B updates?

A/B updates are useful when devices are remote, field access is expensive, power loss is possible, or failed updates would create significant support cost.

<script type="application/ld+json">
{
  "@context":"https://schema.org",
  "@type":"FAQPage",
  "mainEntity":[
    {"@type":"Question","name":"What is firmware rollback?","acceptedAnswer":{"@type":"Answer","text":"Firmware rollback is the ability to return to a previous working firmware image when a new update fails to boot, fails health checks, or creates serious operational problems."}},
    {"@type":"Question","name":"Are signed firmware images enough for secure updates?","acceptedAnswer":{"@type":"Answer","text":"Signed images are important, but they are not enough alone. A secure update design also needs key management, rollback or recovery, debug control, release process, and diagnostics."}},
    {"@type":"Question","name":"When should embedded products use A/B updates?","acceptedAnswer":{"@type":"Answer","text":"A/B updates are useful when devices are remote, field access is expensive, power loss is possible, or failed updates would create significant support cost."}}
  ]
}
</script>
