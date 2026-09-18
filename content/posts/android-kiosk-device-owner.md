---
title: "Android Kiosk Mode and Device Owner for Dedicated Devices"
seo_title: "Android Kiosk Mode and Device Owner for Dedicated Devices"
description: "Build a secure Android kiosk with Device Owner, lock task mode, provisioning, app updates, recovery, offline operation, remote management, and escape testing."
date: 2026-08-29
lastmod: 2026-08-29
draft: false
roadmap_id: "ESB-P013"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["Android kiosk mode", "Android Device Owner", "dedicated Android device", "lock task mode", "Android kiosk app", "industrial Android kiosk"]
cover:
  image: "/images/posts/android-kiosk-device-owner-hero.jpg"
  alt: "Rugged Android kiosk prototype with locked full-screen interface, device management laptop, and embedded computer"
images:
  - "/images/posts/android-kiosk-device-owner-hero.jpg"
---

An Android kiosk is a managed device state, not a full-screen activity. A production kiosk must prevent unintended escape, survive reboot and network loss, receive signed updates, recover from an application failure, preserve device identity, and give authorized technicians a controlled service path.

Device Owner is the foundation for a company-owned dedicated device. A Device Policy Controller (DPC) can allowlist applications for lock task mode, apply restrictions, control selected system UI features, and manage updates. A pinned app without Device Owner is primarily a user convenience and is not equivalent to a managed kiosk.

This guide extends our [embedded firmware and Android BSP resources](/embedded-firmware-bsp/) and the [Android SBC product overview](/posts/android-sbc-overview/).

## Device Owner, DPC, and Lock Task Mode

Use three separate concepts:

- **Device Owner:** the management authority established during provisioning on an unprovisioned device.
- **DPC:** the application that uses Android device-policy APIs.
- **Lock task mode:** the runtime state that restricts users to an allowlisted app or app set.

Android's [lock task mode documentation](https://developer.android.com/work/dpc/dedicated-devices/lock-task-mode) explains that the DPC allowlists packages and can control which system UI features remain available. Build the kiosk application so it can recover its task after a process restart; do not rely on one activity remaining alive forever.

## Choose a Provisioning Path

Provisioning must fit the factory and service model:

- QR code for controlled setup on supported Android versions
- NFC bump for compatible devices
- Zero-touch or enterprise enrollment where the commercial ecosystem supports it
- Factory provisioning integrated into a custom AOSP image
- `adb` only for development, never as the production process

Record a unique device identity, product variant, ownership state, installed DPC version, and enrollment result. Provisioning failure must route to a visible quarantine or service state, not to an unrestricted launcher.

For a custom BSP, coordinate DPC enrollment with the [Android platform customization workflow](/posts/custom-android-bsp-development/), especially Setup Wizard, system apps, permissions, SELinux, and factory reset behavior.

## Decide What the User Can Do

Create a policy matrix instead of applying every restriction:

| Capability | Normal user | Technician | Remote administrator |
|---|---|---|---|
| Leave kiosk app | No | Authenticated service flow | Policy action |
| Change network | Limited or no | Yes, scoped | Yes |
| Adjust volume/brightness | Product dependent | Yes | Optional |
| Install apps | No | Signed service package only | Managed deployment |
| USB data | Normally blocked | Time-limited enable | Policy controlled |
| Factory reset | No | Documented physical/auth flow | High-assurance action |
| View diagnostics | Simple status | Detailed logs/test UI | Fleet telemetry |

Avoid hidden tap sequences as the only service authentication. They are discoverable and difficult to audit. Use a time-limited technician credential, hardware service input, signed challenge, or centrally authorized action.

## Harden the Escape Paths

Test every path that can surface another activity or system UI:

- Home, back, overview, notification shade, quick settings
- Long-press power and reboot menus
- Incoming USB accessory or storage
- Keyboard shortcuts, mouse buttons, and accessibility services
- Settings intents, file pickers, share sheets, permission dialogs
- Crash dialogs, ANR screens, and app update transitions
- Captive portal and Wi-Fi authentication windows
- Safe mode, recovery, bootloader, and external boot media
- Overlay windows and picture-in-picture

Google notes that other apps and services can create windows over a lock-task app; the DPC can apply `DISALLOW_CREATE_WINDOWS` where appropriate. Test with the exact Android release because platform behavior changes.

## Boot and Application Recovery

Define a supervised startup chain:

1. Verified boot starts the approved Android image.
2. Device management confirms ownership and policy.
3. Network comes up if required, with an offline timeout.
4. Kiosk app starts in lock task mode.
5. Health service checks UI, peripherals, storage, and backend status.
6. Watchdog restarts the app or device according to failure class.

An offline kiosk should remain useful when the management server is unavailable. Cache the minimum policy and content required for operation, bound the cache age, and show a clear but non-escapable service state when business data is too old.

## Update the OS and App Separately

The kiosk app and Android system have different release risks. Application updates need signature continuity, data migration, rollback, and a transition that does not expose the launcher. OS updates need boot-slot recovery and BSP regression.

Use the [Android A/B OTA reliability process](/posts/android-ab-ota-updates-embedded-sbc/) for system images. Stage both types of updates by cohort and collect version, health, crash, boot, and rollback telemetry.

Never force an OS update during an active transaction, manufacturing cycle, or safety-sensitive state. The application should expose an “update safe” condition to the management agent.

## Physical and Data Security

- Lock or remove unused external USB and debug ports
- Disable production `adb` and unauthorized bootloader unlock
- Use Verified Boot and production signing keys
- Store device credentials in hardware-backed keystore where available
- Encrypt sensitive local data and minimize retention
- Authenticate all management traffic
- Rate-limit technician and local-admin entry
- Log policy and software changes with trusted time when possible

A kiosk enclosure should also detect or at least resist access to reset, boot-mode, storage, and debug points.

## Acceptance Test Matrix

| Area | Tests | Pass condition |
|---|---|---|
| Enrollment | New, retry, wrong tenant, offline | Only valid devices reach operational state |
| Escape resistance | Keys, gestures, intents, peripherals | No unauthorized system access |
| App failure | Crash, ANR, corrupt cache | Controlled restart or service mode |
| Network | Loss, captive portal, DNS/TLS failure | Bounded offline behavior and clear telemetry |
| Update | App and A/B OS, power loss, rollback | Kiosk remains recoverable and locked |
| Service | Technician login, timeout, audit | Authorized, scoped, and recorded |
| Reset | Factory reset and re-enrollment | Ownership cannot be bypassed |
| Endurance | 72-hour UI/peripheral soak | No memory leak, burn-in issue, or policy drift |

## Deployment Checklist

- [ ] Device Owner is established through a repeatable provisioning flow
- [ ] DPC and kiosk app signing keys have lifecycle and backup procedures
- [ ] Allowlist and system-UI policy match the actual workflow
- [ ] Every known escape path is tested with production peripherals
- [ ] Offline operation and stale-data policy are defined
- [ ] App and OS update paths are separately recoverable
- [ ] Technician access is authenticated, time-limited, and logged
- [ ] Bootloader, recovery, USB, and `adb` states are hardened
- [ ] Watchdog and health checks represent the full product
- [ ] Factory reset returns to managed provisioning, not an open consumer state

## Engineering Sources and Review Notes

Management and lock behavior were checked against Android Developers' official [dedicated-device lock task documentation](https://developer.android.com/work/dpc/dedicated-devices/lock-task-mode) and the broader [dedicated devices guidance](https://developer.android.com/work/dpc/dedicated-devices). Exact APIs and available system-UI controls depend on Android version and management mode; validate on the shipping BSP and Compatibility Test Suite target.

## FAQ

### Is Android screen pinning the same as kiosk mode?

No. Screen pinning is user-controlled. A production dedicated device normally uses a DPC as Device Owner and an allowlisted lock task mode.

### Can a kiosk operate without internet access?

Yes, if policy, credentials, content, and business rules are designed for offline use. Define cache age, reconciliation, and what the device does when trust or data expires.

### Should the kiosk app be a system app?

Not automatically. Device Owner provides many management controls. Use privileged integration only when a documented platform requirement needs it, because system privileges increase security and maintenance responsibility.
