---
title: "Device Tree Overlays for Product Variants"
seo_title: "Device Tree Overlays for Embedded Linux Product Variants"
description: "Use Linux device tree overlays for embedded variants with explicit base DTB compatibility, safe selection, conflict checks, and release validation."
date: 2026-11-11T09:00:00+08:00
lastmod: 2026-11-24T09:00:00+08:00
draft: false
roadmap_id: "ESB-P029"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["device tree overlays","embedded Linux device tree","DTB DTBO compatibility","U-Boot overlays","hardware variant management"]
cover:
  image: "/images/posts/device-tree-overlays-product-variants-hero.jpg"
  alt: "A modular baseboard with a connected display and a separate camera daughterboard on an ESD bench"
  caption: "AI-generated engineering illustration."
images: ["/images/posts/device-tree-overlays-product-variants-hero.jpg"]
---

A single carrier board can ship with two panels, an optional camera, or a different I/O mezzanine. If every combination gets a copied device tree, fixes to shared clocks and regulators tend to land in only some copies. A device tree overlay can keep the shared description together while making a change to one variant explicit.

Treat an overlay as a versioned hardware description. The file has a base it can modify, a list of devices it enables, and an order relative to other overlays. Give it an owner and test it as part of the [embedded Linux BSP](/embedded-firmware-bsp/).

## Decide what belongs in the base tree

| Hardware change | Likely representation | Review question |
|---|---|---|
| Stable power, memory-facing platform setup or boot storage | Base tree and early firmware as applicable | Does early boot rely on it before overlays can be applied? |
| One optional panel or camera | Board-level overlay applied at boot | Does it change pin mux, supplies, clocks or video graph endpoints? |
| Electrically incompatible carrier revision | Separate base DTB or tightly constrained overlay set | Can the wrong configuration drive an unsafe pin or rail? |
| Unrelated software feature flag | Product configuration outside the tree | Does the information describe actual hardware? |

Keep detection independent from optional buses when possible. An EEPROM read over a bus configured by the overlay is a circular dependency. Assemble a small compatibility table from board ID, revision, connector population, base DTB, ordered overlay list and required drivers. Define a documented behavior for unknown or unreadable identities. A safe base can keep optional outputs disabled and expose a service diagnostic when the board permits it.

Review pin assignments and electrical limits against the [carrier board design](/posts/compute-module-carrier-board-design/) and include the variant set in the [device tree review](/posts/device-tree-review-checklist/).

{{< figure src="/images/posts/device-tree-overlays-product-variants-workflow.svg" link="/images/posts/device-tree-overlays-product-variants-workflow.svg" alt="Board identity maps to a validated base DTB and an ordered set of overlays before Linux boots" caption="Resolve hardware identity before applying the tested overlay set." >}}

## Check targets, symbols, and the merged tree

For an overlay targeting a base label, compile the base with its required symbols and preserve the label as a compatibility boundary. A target path can be used where the tool and layout support it, but paths can change with tree structure. A label or path that still resolves can point to hardware with different capabilities after a BSP upgrade. Inspect the resulting tree after a successful composition.

The kernel overlay notes describe how overlays change the live tree and can add or remove device nodes. They also describe targeting by label or by path. U-Boot documents pre-kernel application and the workspace required when applying overlays manually. For fixed product variants, select the base and overlay set during boot and test the resulting tree before using live changes. [Linux kernel overlay notes](https://docs.kernel.org/devicetree/overlay-notes.html) · [U-Boot overlay documentation](https://docs.u-boot.org/en/latest/usage/fdt_overlays.html).

Use offline composition as a build check. For a configured host toolchain, a typical validation command is:

```sh
fdtoverlay -i base.dtb -o merged.dtb panel.dtbo camera.dtbo
dtc -I dtb -O dts -o merged.dts merged.dtb
```

Run relevant binding checks as part of the kernel build and inspect the merged device tree where checks are available for the chosen tooling. Look for two devices claiming one I²C address, conflicting pin functions, ports pointing at the wrong endpoint, and alternate devices left enabled after replacement. Record the order explicitly. A fix in one overlay can change what a later one sees.

For example, two panel options might share a controller and change the endpoint, enable GPIO and backlight supply. Mark those overlays mutually exclusive in the variant manifest. Add a composition test for each valid panel, a base-only test, and a rejected configuration containing both. For every valid variant, compare the enabled nodes, resolved supplies and endpoints with the wiring drawing.

## Validate selection on the board

1. Boot each populated variant with its expected firmware and capture the board identity, base identifier and selected overlay order.
2. Check the bootloader and Linux logs for overlay and probe results. Examine the live tree and confirm the actual driver binds with the expected resources.
3. Exercise the full peripheral path. A camera probe does not verify stream format, frame drops, bandwidth or suspend recovery.
4. Boot with a missing, malformed or unknown board ID and check the documented degraded state on a controlled test unit.
5. Repeat on the upgraded and rollback image. Pair DTB and DTBO artifacts with their compatible BSP and kernel branch.

Connect selection logs and the compatibility matrix to the [Yocto BSP layers](/posts/yocto-layer-architecture-product-bsp/) that deliver the images. Sign or otherwise authenticate the selected artifacts under the product's boot policy. For Android products, check where the vendor integrates DTB or DTBO in its [Android boot chain](/posts/android-boot-flow-partition-layout/) instead of assuming a U-Boot Linux workflow.

A release engineer should be able to identify the exact tree that booted from a unit ID and image version. That trace is especially valuable when two physically similar boards show different behavior after the same update.
