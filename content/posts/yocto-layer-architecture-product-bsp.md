---
title: "Yocto Layer Architecture for a Maintainable Product BSP"
seo_title: "Yocto Layer Architecture: Maintainable Product BSP Design"
description: "Organize Yocto BSP, distro, and product layers with clear ownership, scoped overrides, pinned revisions, and CI checks that make upgrades reproducible."
date: 2026-10-14
lastmod: 2026-10-27
draft: false
roadmap_id: "ESB-P022"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["Yocto layer architecture", "Yocto BSP development", "Yocto product layer", "BitBake layer maintenance", "embedded Linux reproducible builds"]
cover:
  image: "/images/posts/yocto-layer-architecture-product-bsp-hero.jpg"
  alt: "Illustrative Yocto development bench with display, networking, and camera SBC variants beside a laptop"
  caption: "AI-generated engineering illustration of a multi-product BSP workspace."
images: ["/images/posts/yocto-layer-architecture-product-bsp-hero.jpg"]
---

A Yocto product becomes difficult to maintain when nobody can explain where a setting belongs. The kernel patch lives in a vendor checkout, an image package is added through one developer's `local.conf`, and a customer feature is enabled by a global override that also changes the recovery image. The build may work today, but the next BSP upgrade turns into archaeology.

Good layer architecture makes ownership and scope visible. Hardware support should be distinguishable from distribution policy and product composition. The purpose is not to create as many layers as possible; it is to make the next change predictable.

This guide is part of our [embedded Linux BSP development](/embedded-firmware-bsp/) resources. Examples follow the documented Yocto layer model; use syntax and compatibility checks appropriate to your selected release. No support-lifetime claim is implied by the documentation branch used here.

## Separate Three Decisions

Ask three questions before deciding where a change belongs:

| Decision | Examples | Usual home |
|---|---|---|
| What hardware is present? | Machine configuration, kernel/device-tree selection, bootloader support | BSP or board layer |
| What system policy applies? | Init system, security defaults, distribution features | Distro layer/configuration |
| What does this product deliver? | Application packages, image variants, product service configuration | Product layer |

The [Yocto BSP guide](https://docs.yoctoproject.org/scarthgap/bsp-guide/bsp.html) describes a BSP as the hardware-support information and software needed for a platform, including bootloader, kernel, device tree, and drivers. This is a useful boundary: a customer dashboard is not a board-support responsibility just because it runs on that board.

For a small team, one company layer can contain clearly separated directories initially. Split it when there is a real ownership or reuse boundary. Five thin layers maintained by the same person are not inherently better than one well-structured layer.

## A Practical Repository Layout

The following is an illustrative arrangement, not a required directory naming standard:

```text
sources/
  poky/                    # Pinned upstream revision
  meta-soc-vendor/          # Pinned vendor BSP
  meta-company-board/      # Machine support and hardware patches
    conf/machine/
    recipes-bsp/
    recipes-kernel/
  meta-company-distro/     # Shared operating-system policy
    conf/distro/
  meta-company-product/    # Applications and image composition
    recipes-core/images/
    recipes-core/packagegroups/
    recipes-apps/
release-manifest/          # Revisions and build configuration
```

Keep build output and local experimentation outside those source repositories. Document the owner of every layer and the reason each dependency exists. When a product image depends on a proprietary accelerator runtime, make that dependency explicit rather than relying on the runtime happening to be installed by a vendor demo image.

{{< figure src="/images/posts/yocto-layer-architecture-product-bsp-workflow.svg" link="/images/posts/yocto-layer-architecture-product-bsp-workflow.svg" alt="Hardware, distro policy, and product composition layers feed a pinned build manifest that produces validated images" caption="Layers express responsibility. The release manifest selects the compatible revisions used together." >}}

## Use Appends Without Creating Invisible Global Changes

Use recipe appends for deliberate customizations, and scope machine-specific changes to the appropriate machine. A patch intended for one carrier board should not alter every board sharing the same SoC. Likewise, a development-only debug package should not enter production and recovery images through a broad package setting.

The official [Yocto layer manual](https://docs.yoctoproject.org/scarthgap/dev-manual/layers.html) explains layer configuration, dependencies, compatible release series, and recipe appends. Layer priority participates in recipe selection, but it is not a universal override switch for all metadata. Inspect the resolved build rather than assuming that a larger priority number makes every setting win.

Useful inspection commands include:

```sh
bitbake-layers show-layers
bitbake-layers show-appends
bitbake-layers show-overlayed
bitbake -e product-image
```

Run them in the configured build environment. The final command can produce a large environment dump; review the variables relevant to your question and avoid publishing output containing private paths or credentials. For a kernel issue, inspect the selected kernel recipe and its resolved variables, not only the image recipe.

Treat unmatched appends and unexpected provider changes as review failures. A vendor recipe rename during an upgrade can leave the build successful while your intended customization no longer applies.

## Keep Vendor Changes Reviewable

Avoid editing a vendor layer in place without a recorded patch strategy. Prefer a company-owned append or a reviewed upstream/vendor contribution when suitable. If a fork is unavoidable, maintain a small, documented patch series and record why each patch still exists.

For every carried patch, retain the affected component version, upstream status, product reason, test coverage, and removal condition. A patch that was necessary for an old kernel may become redundant or conflict with a later fix. “It has always been there” is not a sufficient reason to keep it.

Combine this process with a [device-tree review workflow](/posts/device-tree-review-checklist/). Device-tree changes are especially easy to carry forward mechanically even after power sequencing, pin assignments, or supported peripherals have changed.

## Pin the Inputs That Define a Release

A release manifest should name exact source revisions for all layers and application repositories, the selected machine and distro, image targets, configuration templates, and relevant toolchain/build-environment information. Avoid floating revisions such as `AUTOREV` in a production release path.

Pinning is necessary but not sufficient for bit-for-bit reproducibility. Source downloads, generated timestamps, host-tool leakage, external signing, and nondeterministic build steps can still matter. Describe the level you have actually verified: reconstructable inputs, functionally equivalent image, or identical output bytes.

Shared download and build-state caches improve turnaround, but they should not be the only place required inputs survive. Keep controlled source mirrors and an artifact-retention policy suitable for the product lifetime. A clean rebuild is the test that exposes a dependency on an engineer's old workspace.

Do not store production signing keys or reusable factory credentials in a layer. The build can describe where a signing step occurs without embedding its secrets. Tie release artifacts to the [firmware signing and rollback process](/posts/secure-firmware-update-rollback/) and preserve their hashes.

## CI Should Test Boundaries, Not Only Compilation

Use a matrix of supported machines and image variants. At minimum, check parsing, expected package composition, selected providers, appends, image creation, and a target smoke test. Hardware-dependent behavior needs hardware evidence; successful compilation cannot establish camera, suspend, or boot reliability.

| Check | Regression it can expose |
|---|---|
| Machine/image matrix | An override unintentionally affecting another board |
| Package manifest comparison | Debug tools or unwanted services entering production |
| Patch and provider review | A vendor update bypassing a customization |
| Clean-source rebuild | Missing downloads or undeclared local inputs |
| Target boot and peripheral test | A build that no longer operates the hardware |
| SBOM and vulnerability review | New components or changed security exposure |

Keep findings connected to the [embedded Linux SBOM and CVE workflow](/posts/sbom-cve-response-embedded-linux/). A technically clean layer layout does not itself keep software secure; it makes ownership and updates easier to manage.

## Upgrade One Boundary at a Time

Before a major upgrade, capture the current image manifests, boot timing, peripheral behavior, and known deviations. Update the layer set coherently, then examine the differences rather than simply fixing parse errors until the build turns green.

The [production Yocto versus Buildroot comparison](/posts/yocto-vs-buildroot-production-embedded-linux/) helps frame whether this maintenance structure matches the team's needs. Once Yocto is chosen, the goal is not minimum metadata at any cost. It is a product BSP whose behavior another engineer can explain, rebuild, test, and update without relying on undocumented local knowledge.
