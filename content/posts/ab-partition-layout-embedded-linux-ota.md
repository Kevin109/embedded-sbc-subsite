---
title: "A/B Partition Layout for Reliable Embedded Linux OTA"
seo_title: "A/B Partition Layout for Reliable Embedded Linux OTA Updates"
description: "Design reliable embedded Linux A/B partitions with boot counters, signed artifacts, data migration, recovery, storage sizing, and power-loss tests."
date: 2026-08-05
lastmod: 2026-08-05
draft: false
roadmap_id: "ESB-P009"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["A/B partition layout", "embedded Linux OTA", "dual rootfs update", "RAUC A/B update", "Linux rollback partition", "fail-safe firmware update"]
cover:
  image: "/images/posts/ab-partition-layout-embedded-linux-ota-hero.jpg"
  alt: "Embedded Linux A/B update lab with two storage slots, power-cut relay, SBC, and serial console"
images:
  - "/images/posts/ab-partition-layout-embedded-linux-ota-hero.jpg"
---

An A/B partition layout gives an embedded Linux product two bootable system states: the device runs from one slot while an updater writes the other. That redundancy is valuable, but it is not sufficient. A reliable design also needs atomic boot selection, signed artifacts, boot-attempt accounting, product health confirmation, compatible persistent data, and a service path when both slots fail.

The design should be written as a state machine before a partition table. That prevents a common failure: two complete root filesystems exist, but the bootloader, updater, and application disagree about which one is good.

This guide belongs to the [embedded firmware and BSP lifecycle](/embedded-firmware-bsp/) and complements our [secure embedded update architecture](/posts/secure-firmware-update-rollback/).

## A Practical Layout

A common eMMC or NVMe layout is:

| Region | Redundancy | Purpose |
|---|---|---|
| Bootloader/firmware | Fixed or redundantly managed | SoC initialization and boot selection |
| Boot A | A/B | Kernel, device tree, initramfs or unified image |
| Rootfs A | A/B | Read-only or controlled system image |
| Boot B | A/B | Alternate boot artifacts |
| Rootfs B | A/B | Alternate system image |
| Data | Shared | Configuration, application data, logs |
| Recovery | Optional fixed | Service tools or last-resort image |
| Metadata | Redundant/atomic | Slot priority, attempts, version, status |

Some products combine boot and root filesystem artifacts; some use a content-addressed deployment model rather than block-image slots. OSTree, for example, describes [atomic filesystem-tree upgrades](https://ostreedev.github.io/ostree/atomic-upgrades/). Choose the model that matches storage, bootloader, and support needs.

## Size from Future Images

Each system slot must fit the largest image expected over the product life, not today's compressed artifact. Include filesystem overhead, growth, alignment, and update format.

Use:

`slot size ≥ current uncompressed image × growth factor + filesystem reserve`

A reasonable planning exercise tests 1.5× and 2× growth scenarios. Also reserve space for:

- Downloaded update or streaming buffer
- Delta-update scratch space if required
- Rollback metadata and signatures
- Crash dumps during update testing
- Shared data growth and free-space margin

The [embedded storage endurance and capacity guide](/posts/embedded-sbc-storage-reliability/) helps connect layout size to write amplification and device life.

## Define the Boot State Machine

Use explicit states:

1. **Good:** previously confirmed slot.
2. **Candidate:** fully written and verified; selected for next boot.
3. **Trying:** candidate is booting with attempts remaining.
4. **Confirmed:** product health checks passed; candidate becomes good.
5. **Bad:** attempts exhausted or health service rejected the slot.

The bootloader must decrement attempts in a way that survives resets. The operating system should confirm only after essential services are ready. RAUC's [bootloader interaction documentation](https://rauc.readthedocs.io/en/latest/using.html#marking-a-boot-as-successful-or-failed) uses the same principle: installation is not complete until the new system is confirmed operational.

Do not reset the counter early in startup. If the main application, storage mount, or network identity fails after confirmation, the device can remain stuck on a technically bootable but unusable release.

## Keep Shared Data Rollback-Safe

The root filesystems can roll back; the shared data partition may already have been changed by the new release. Use one of these strategies:

- Backward-compatible schema migrations across the supported rollback window
- Expand/contract migration: add new fields first, remove old fields in a later release
- Versioned data directories with controlled activation
- Snapshot before migration, if storage and filesystem make it reliable
- Migration journal that can resume or reverse after power loss

Separate immutable factory identity from ordinary configuration. Protect keys, serial numbers, calibration, and manufacturing records from both OTA replacement and user-data cleanup.

## Bootloader and Update Framework

Select a framework with a documented relationship to the bootloader. RAUC supports A/B slots and explicit good/bad state; its [design checklist](https://rauc.readthedocs.io/en/latest/checklist.html) covers boot failure, retry, watchdog, PKI, and data-migration decisions. Mender, SWUpdate, and vendor frameworks can also be valid, but compare actual features on the target bootloader and storage.

Review:

- Where slot metadata is stored and how it is updated atomically
- Whether metadata has redundant copies and corruption detection
- How power loss during metadata update behaves
- How the currently running slot is identified
- Whether bootloader environment wear is bounded
- How bootloader updates are made safe
- What happens if every slot is marked bad

Bootloader updates deserve a separate threat and failure analysis. A/B root filesystems do not automatically protect a single bootloader region.

## Artifact Security

Every update bundle should carry product compatibility, version, cryptographic signature, hashes, and a manifest of components. Verification must occur before activation, with trust anchored outside a writable root filesystem.

Define downgrade policy separately from failure rollback. Returning automatically to the immediately previous trusted slot after a failed boot is different from allowing an operator to install any older vulnerable release.

For Android products, the related [Android A/B OTA production workflow](/posts/android-ab-ota-updates-embedded-sbc/) covers AVB, `update_engine`, and Virtual A/B specifics.

## Power-Loss and Corruption Test Plan

Use a programmable power switch and repeat each stage many times:

| Stage | Injection | Pass condition |
|---|---|---|
| Download | Random cut | Running slot unchanged; download restarts/resumes |
| Write inactive boot | Random cut | Good slot remains selectable |
| Write inactive rootfs | Random cut | Partial slot never becomes candidate |
| Verify/signature | Corrupt bytes | Activation is refused |
| Update metadata | Random cut | At least one valid metadata copy remains |
| First candidate boot | Kernel panic/watchdog | Attempts decrease and fallback occurs |
| Health check | Application failure | Candidate is not confirmed |
| Data migration | Random cut | Migration resumes or rollback still reads data |

Also test worn storage, full data partition, incorrect hardware compatibility, skipped-version updates, and a device that remains offline for months.

## Release Acceptance Checklist

- [ ] Slot sizes include documented lifetime growth
- [ ] Active slot is never written during a normal OTA
- [ ] Boot selection metadata is atomic or redundant
- [ ] Boot attempt and confirmation behavior is deterministic
- [ ] Health checks represent the product, not only Linux startup
- [ ] Shared data can survive upgrade, power loss, and rollback
- [ ] Update bundles are signed and hardware-compatible
- [ ] Downgrade policy and emergency rollback are distinct
- [ ] Bootloader update and recovery are explicitly designed
- [ ] Randomized power-loss testing passes on production storage

## Engineering Sources and Review Notes

Slot, boot-confirmation, watchdog, and recovery guidance was checked against the [current RAUC documentation](https://rauc.readthedocs.io/en/latest/), especially its [design checklist](https://rauc.readthedocs.io/en/latest/checklist.html). Atomic deployment concepts were also checked against the [OSTree upgrade model](https://ostreedev.github.io/ostree/atomic-upgrades/). Partition sizes, retry counts, and health criteria are product decisions and require target-hardware validation.

## FAQ

### Does A/B require two complete copies of every partition?

No. Many designs duplicate only bootable system artifacts and keep a shared data partition. Every shared mutable component must be analyzed because it can affect both slots.

### How many boot attempts should a candidate receive?

Enough to tolerate expected transient failures but few enough to recover promptly. Two or three is common, but the correct value depends on startup time, watchdog behavior, and service requirements.

### Is a separate recovery partition still useful?

It can be valuable for service, factory restore, or recovery when both normal slots fail. It also consumes storage and needs its own update and security policy.
