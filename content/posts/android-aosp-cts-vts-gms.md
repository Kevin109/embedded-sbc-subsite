---
title: "AOSP, CTS, VTS, and GMS: What Embedded Android Teams Need"
seo_title: "AOSP vs GMS, CTS & VTS for Embedded Android"
description: "Decide whether an embedded Android product needs AOSP, Android compatibility, or Google Mobile Services, then plan CTS, VTS, and release evidence."
date: 2026-10-26T09:00:00+08:00
lastmod: 2026-11-24T09:00:00+08:00
draft: false
roadmap_id: "ESB-P025"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["AOSP vs GMS","Android CTS","Android VTS","embedded Android compatibility","Google Mobile Services licensing"]
cover:
  image: "/images/posts/android-aosp-cts-vts-gms-hero.jpg"
  alt: "Embedded touchscreen devices and an Android development board on a validation bench"
  caption: "AI-generated engineering illustration."
images: ["/images/posts/android-aosp-cts-vts-gms-hero.jpg"]
---

An embedded Android team can bring up a display, install its own APK, and still be months away from delivering the software package promised to a customer. The schedule changes when the customer expects Google Play, managed provisioning, or third-party apps that call Google APIs. Put those dependencies in the product requirements before you choose a board or commit to a release date.

Start with the app inventory and the installation method. Identify the APIs each app calls, who signs it, how operators provision a replacement unit, and what must work with no internet access. Translate those answers into a release path with your [firmware BSP team](/embedded-firmware-bsp/).

Write acceptance tests in the buyer's language. For a retail terminal, “a store technician can replace a failed unit and sign into the POS app within ten minutes” gives the team a concrete provisioning scenario. Capture whether the app requires Google APIs on first login, during silent updates, or only for optional features. The guide to [embedded Android boards](/posts/android-sbc-overview/) introduces the platform choices that precede this evaluation.

## What AOSP, CDD, CTS, VTS, and GMS each cover

| Term | What it gives the project | What to put in the release folder |
|---|---|---|
| AOSP | Android platform source and build inputs | Source manifest, vendor components and license inventory |
| CDD | Compatibility requirements for the chosen Android release and applicable device category | Requirement matrix with an owner and evidence per clause |
| CTS | Tests of Android compatibility behavior | Results tied to build fingerprint, suite version, and unit |
| VTS | Vendor-facing validation, including HAL and kernel tests | Results mapped to the relevant BSP, vendor image and kernel |
| GMS | Google apps and APIs offered through a separate licensing process | Written confirmation for the intended commercial configuration |

Google describes Android compatibility as building from AOSP, meeting the applicable CDD, and passing the corresponding CTS. The CDD includes requirements that automated tests cannot fully exercise. The VTS documentation describes tests against HAL and kernel behavior. Review which suites apply to the product and branch when you plan the lab. [Android compatibility overview](https://source.android.com/docs/compatibility/overview) · [CDD by release](https://source.android.com/docs/compatibility/cdd) · [VTS documentation](https://source.android.com/docs/core/tests/vts).

{{< figure src="/images/posts/android-aosp-cts-vts-gms-workflow.svg" link="/images/posts/android-aosp-cts-vts-gms-workflow.svg" alt="Decision flow from APK dependencies and distribution to compatibility evidence and GMS licensing" caption="App dependencies and distribution determine what the release team needs to verify." >}}

## Choose a path from the app requirements

**A private, offline kiosk.** List the required APKs and test them on the intended system image, with the network disconnected and without signing in. Include barcode scanners, USB permissions, accessibility controls, and the exact policy that keeps the operator in the application. A package that runs from a developer shell can still fail under the actual device-owner policy. Test provisioning on a wiped unit using the same [Android kiosk provisioning](/posts/android-kiosk-device-owner/) process the installer will use.

**A device that promises Android compatibility.** Map the product's declared features and device category to the release-specific CDD. Run CTS early against the first credible system image, then again as the production configuration stabilizes. Track manual evidence for physical behavior the suite cannot establish. Set aside units and operator time for repeat tests, rather than treating final validation as a single overnight job.

**A device that needs Google Play or Google APIs.** Identify the precise requirement. APK installation through Play, Maps SDK behavior, Google sign-in, and enterprise device enrollment are separate product dependencies. Ask the responsible commercial partner to confirm which build, markets, SKUs, update process, and software package fall under the intended arrangement. Passing CTS establishes part of the compatibility evidence. Google describes compatible devices as eligible to seek GMS licensing. [Google Mobile Services](https://www.android.com/gms/) · [Android compatibility FAQ](https://source.android.com/docs/compatibility/compatibility-faq).

## Put the test plan into the supplier statement of work

Specify who supplies the kernel, vendor blobs, graphics and camera HALs, platform branch, test harness, reference hardware, and bug fixes. Ask for the complete release manifest, test suite versions and logs, plus a reproduction path for each open failure. A result for the supplier's reference kit needs review when the final board swaps a panel, disables a radio, or changes its vendor image.

Route failures to the team that can act on them. A camera feature advertised without the corresponding behavior usually needs a joint review of configuration and HAL implementation. A graphics regression may sit below the app in the compositor or GPU stack. A passing rerun on one unit leaves the original failure unexplained. Keep both runs, identify the change, and decide whether it solved the problem. Plan this alongside [Android BSP development](/posts/custom-android-bsp-development/).

## Evidence to review on the shipping build

- Record the hardware revision, BOM variant, build fingerprint, suite versions, test environment, run date and unexecuted coverage.
- Verify a wiped unit, a normal boot after update, and an interrupted update on the locked production configuration.
- Exercise app install, initial provisioning, offline startup, peripheral permissions and replacement-unit setup.
- Preserve compatibility results separately from field functional tests and written distribution or licensing evidence.
- Reopen affected test evidence after a change to the vendor image, HAL, board configuration, feature declarations, signing policy or product APK.

Review the [Android boot chain](/posts/android-boot-flow-partition-layout/) and [Android OTA rollback](/posts/android-ab-ota-updates-embedded-sbc/) together when changes span multiple images. A release reviewer should be able to identify exactly which software and hardware the evidence covers, which app promises it supports, and who can authorize the next update.
