---
title: "Android A/B OTA Updates on Embedded SBCs"
seo_title: "Android A/B OTA Updates for Embedded SBCs: Production Guide"
description: "Design reliable Android A/B OTA updates for embedded SBCs, including slot control, AVB signing, rollback, power-loss recovery, fleet rollout, and validation."
date: 2026-07-08
lastmod: 2026-07-08
draft: false
roadmap_id: "ESB-P004"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["Android A/B OTA update", "embedded Android OTA", "Android seamless update", "Android SBC update", "Virtual A/B", "Android update_engine", "AVB rollback"]
cover:
  image: "/images/posts/android-ab-ota-updates-embedded-sbc-hero.jpg"
  alt: "Android A/B OTA validation bench with an embedded SBC, touchscreen, recovery cable, and power-cycle relay"
images:
  - "/images/posts/android-ab-ota-updates-embedded-sbc-hero.jpg"
---

An Android A/B OTA update is reliable only when the whole boot chain agrees on which slot is active, which image is trusted, how many failed boots are allowed, and when the new release is healthy enough to become permanent. Writing a payload to the inactive slot is the easy part. The product work is in power-loss behavior, key management, data migration, health confirmation, rollback policy, and fleet observability.

For an embedded SBC used in a kiosk, HMI, medical terminal, or unattended gateway, the goal is not merely a successful update in the lab. The goal is that a remote device either starts the new release or returns to a known working release without a technician.

This guide complements our [embedded firmware and BSP engineering resources](/embedded-firmware-bsp/) and the broader [secure firmware update and rollback workflow](/posts/secure-firmware-update-rollback/).

## What A/B Actually Protects

In a legacy A/B design, boot-critical partitions exist in slot A and slot B. The running system reads from the active slot while `update_engine` writes the OTA payload to the inactive slot. After verification, the bootloader tries the new slot. If it never reaches a confirmed healthy state, boot-control metadata should direct the next boot back to the previous slot.

Google's [A/B update documentation](https://source.android.com/docs/core/ota/ab) describes the important distinction between **bootable** and **successful**. A newly written slot may be bootable, but it should not be marked successful until Android and the product application have passed their startup checks.

Modern Android releases commonly use Virtual A/B, which keeps redundant boot-critical partitions while using snapshots for large dynamic partitions. The [official OTA overview](https://source.android.com/docs/core/ota) explains the storage trade-off. Do not copy a legacy partition table into a current Android product without checking the exact AOSP and silicon-vendor implementation.

| Protection | A/B helps | A/B does not solve by itself |
|---|---|---|
| Interrupted image write | Active slot remains untouched | Corrupt shared data or boot metadata |
| Bad new kernel or system image | Bootloader can return to old slot | An application that boots but is functionally broken |
| Image tampering | Works with AVB verification | Signing-key custody and release authorization |
| Vulnerable old software | AVB can enforce rollback indexes | Product policy for emergency downgrade |
| Fleet deployment | Enables staged rollout | Telemetry, campaign control, bandwidth, support |

## Partition and Boot-Control Design

Treat the partition table, bootloader, Android build, and updater as one versioned interface. At minimum, review:

- Which partitions are slotted and which are shared
- Whether the product uses legacy A/B or Virtual A/B
- The size of `super`, snapshot working space, metadata, and user data
- Bootloader support for slot priority, retry count, and successful state
- Recovery or fastboot path when neither slot is usable
- Storage reserved for the largest expected full payload
- How factory flashing initializes both slots and boot metadata

The related [reliable A/B partition design for embedded Linux](/posts/ab-partition-layout-embedded-linux-ota/) provides a storage budget and failure-state method that is also useful when reviewing Android layouts.

Do not update a shared, boot-critical partition without an atomic or redundant plan. If both slots depend on one mutable bootloader, device tree, or firmware component, that component can defeat the recovery promise.

## AVB, Signing, and Rollback Protection

Android Verified Boot establishes a chain of trust from a hardware-protected root through boot and verified partitions. The [AOSP Verified Boot guide](https://source.android.com/docs/security/features/verifiedboot) also explains rollback protection: trusted storage records a minimum acceptable version so an attacker cannot simply reinstall a signed but vulnerable build.

A production design should separate:

1. Development and production roots of trust
2. Offline release-signing authority and online distribution services
3. OTA payload signing and partition verification keys where the platform uses both
4. Key rotation, revocation, backup, and recovery procedures
5. Rollback indexes for components that can be updated independently

The operational controls are covered in our [embedded secure-boot key management guide](/posts/secure-boot-key-management-embedded-products/). Never ship engineering keys, and never make an irreversible rollback-index change before the candidate release has passed the intended rollout gate.

## Define “Healthy” at the Product Level

Android reaching the launcher is not a sufficient health check for a dedicated device. Confirm the new slot only after the minimum service set is working:

- Data partition mounted and schema migration completed
- Main application started and responsive
- Display and touch initialized
- Required Ethernet, Wi-Fi, cellular, or fieldbus interface available
- Device identity and secure storage accessible
- Watchdog service active
- Update agent can report the new version and slot

Use a bounded confirmation window. If a kiosk application repeatedly crashes while Android itself stays alive, the health service should withhold confirmation and allow the boot-attempt policy to roll back.

Data migrations need special care. A backward-incompatible database migration can make the old slot boot successfully but leave it unable to read shared user data. Prefer backward-compatible migrations, staged schema changes, or explicit data snapshots with a tested downgrade path.

## Production Rollout Sequence

A safe campaign is a controlled experiment, not a file broadcast.

1. Build from pinned source and archive the target files, payload, symbols, SBOM, and test evidence.
2. Sign through the production release process.
3. Test clean installs, incremental updates, skipped-version updates, and full-payload fallback.
4. Deploy to internal devices representing every board, storage, modem, and display variant.
5. Release to a small canary group with telemetry and a stop condition.
6. Increase cohorts only when install, boot, rollback, crash, storage, and thermal metrics remain within limits.
7. Keep the previous signed release and recovery instructions available until the campaign closes.

Useful fleet metrics include download failures, payload verification failures, install duration, reboot latency, active slot, boot attempts, rollback count, application health, free space, and update-agent version.

## Failure-Injection Test Matrix

Run failures deliberately. A single successful update proves very little.

| Test | Injection point | Pass condition |
|---|---|---|
| Power loss during download | 10%, 50%, 95% | Current slot boots; download resumes or restarts safely |
| Power loss during write | Each major partition phase | Current slot remains bootable; inactive slot is rejected or safely resumed |
| Corrupt payload | Before verification | Installation is refused and reason is logged |
| Wrong product payload | Manifest compatibility check | Payload never reaches the write stage |
| Bad new kernel | First boot | Retry budget expires and previous slot boots |
| App crash loop | Before health confirmation | New slot is not marked successful |
| Full storage | Download and snapshot creation | Update stops without damaging active system |
| Network loss | Repeated disconnects | No duplicate campaign state or partial activation |
| Rollback attempt | Older signed build | Policy accepts or rejects exactly as specified |
| Both slots invalid | Boot selection | Device enters documented service recovery |

Repeat power interruption with a programmable relay at randomized times. Test cold and hot units, worn storage samples, and the final power supply. The update path should also be included in the [embedded SBC validation checklist](/posts/embedded-sbc-product-validation-checklist/).

## Release Acceptance Checklist

- [ ] Correct target and hardware compatibility are enforced
- [ ] Payload and AVB signatures verify with production trust anchors
- [ ] Slot retry and success behavior is documented
- [ ] Product-level health checks gate confirmation
- [ ] Database and configuration migration can survive rollback
- [ ] Power loss has been injected across download, write, boot, and confirmation
- [ ] Full and incremental OTA paths are tested
- [ ] Recovery works when neither normal slot boots
- [ ] Fleet telemetry can identify release, slot, and failure stage
- [ ] Staged rollout has measurable pause and rollback thresholds

## Engineering Sources and Review Notes

Architecture and terminology were checked against the [AOSP A/B system update documentation](https://source.android.com/docs/core/ota/ab), [OTA update overview](https://source.android.com/docs/core/ota), [partition architecture](https://source.android.com/docs/core/architecture/partitions), and [Verified Boot design](https://source.android.com/docs/security/features/verifiedboot). Product health gates, rollout cohorts, and failure-injection criteria are engineering recommendations and must be adapted to the bootloader, storage device, Android release, and regulatory context of the final product.

## FAQ

### Does A/B OTA guarantee that an Android device cannot be bricked?

No. It reduces risk by preserving a known slot, but shared boot components, bad boot metadata, storage failure, broken data migration, or an untested recovery path can still make the product unavailable.

### When should an Android slot be marked successful?

Only after Android and the product's essential services have passed defined health checks. Marking it successful immediately after the kernel boots removes much of the value of automatic rollback.

### Should a new product use legacy A/B or Virtual A/B?

Start from the implementation supported by the target Android release and silicon-vendor BSP. Validate snapshot space, merge behavior, bootloader integration, and power-loss recovery on the actual storage device.
