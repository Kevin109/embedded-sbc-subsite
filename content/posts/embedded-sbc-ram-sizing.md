---
title: "How Much RAM Does an Embedded SBC Really Need?"
seo_title: "Embedded SBC RAM Sizing: Measure Linux Memory Headroom"
description: "Size RAM for an embedded Linux SBC using Linux memory pressure, container peaks, camera buffers, and a combined-workload acceptance test."
date: 2026-10-30T09:00:00+08:00
lastmod: 2026-11-24T09:00:00+08:00
draft: false
roadmap_id: "ESB-P026"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["embedded SBC RAM sizing","embedded Linux memory budget","Linux memory pressure","CMA camera buffers","container memory sizing"]
cover:
  image: "/images/posts/embedded-sbc-ram-sizing-hero.jpg"
  alt: "Soldered memory packages and a processor on a compact embedded single-board computer"
  caption: "AI-generated engineering illustration."
images: ["/images/posts/embedded-sbc-ram-sizing-hero.jpg"]
---

Picking RAM from idle usage is a good way to miss the most expensive minute in an embedded product's day. An HMI may normally show one screen, then briefly hold a browser renderer, two camera buffers, a container restart and an update decompressor at the same time. That overlap matters more than a quiet desktop screenshot.

Size the least expensive memory option that meets product latency and reliability goals under realistic concurrency. Start with the workloads in your [embedded SBC design](/embedded-sbc/), then confirm the exact memory configurations the vendor can supply. Include the release firmware, display mode, browser, model and camera parameters in the test record.

## Put each memory consumer in the right bucket

| Bucket | What to measure | Common accounting trap |
|---|---|---|
| Firmware and reserved regions | Installed RAM, Linux-visible RAM and board carveouts | Subtracting an already excluded region again |
| OS and services | Kernel, base services, reclaim behavior | Treating every cached page as pinned |
| UI and user processes | Private memory, shared pages and cold-start peak | Adding RSS across processes that share libraries |
| Containers | cgroup current, peak and limit events | Adding parent and child cgroup totals |
| Capture and display | Actual buffer sizes, plane count, queue depth, ownership | Counting shared DMA allocations for every consumer |
| Updates and fault recovery | Decompression, process restart overlap, log growth | Testing recovery with usual services stopped |

Collect `/proc/meminfo` over time. `MemAvailable` estimates memory available to new workloads without swapping. Record `CmaTotal` and `CmaFree` when present and verify allocations on the actual driver path. A CMA area may be represented in Linux's memory accounting and can contain movable pages. Confirm how the BSP reserves it before adding or subtracting it in a budget. [Linux proc filesystem documentation](https://docs.kernel.org/filesystems/proc.html).

On systems using cgroup v2, check each workload's `memory.current`, `memory.peak` where available, and changes in `memory.events`. View `/proc/pressure/memory` when enabled to see time lost to memory contention. A unit can meet a free-memory threshold while reclaim causes unacceptable input latency. [Linux cgroup v2 memory controller](https://docs.kernel.org/admin-guide/cgroup-v2.html) · [Linux pressure stall information](https://docs.kernel.org/accounting/psi.html).

{{< figure src="/images/posts/embedded-sbc-ram-sizing-workflow.svg" link="/images/posts/embedded-sbc-ram-sizing-workflow.svg" alt="RAM sizing flow from installed memory through measured overlapping consumers and release checks" caption="Build the memory budget using disjoint allocations, then confirm it with pressure and latency measurements." >}}

## Worked example for a two-camera Linux gateway

Use MiB throughout and treat these values as planning inputs. Suppose the two capture streams allocate six tightly packed NV12 buffers each at 1920 × 1080. Their lower-bound payload is `12 × 1920 × 1080 × 1.5` bytes, or about 35.6 MiB. Real allocations can be larger. Obtain plane sizes and strides from the driver, and count the actual shared or copied buffers as described in the [camera memory budget](/posts/multi-camera-bandwidth-memory-edge-vision/).

Assume the rest of the design uses 230 MiB for the OS and services, 370 MiB for app and UI peaks, 170 MiB for an update worker, and 64 MiB for distinct media allocations including alignment and display buffers. An example 25% planning margin on 834 MiB adds about 209 MiB. The candidate needs roughly 1,043 MiB of usable RAM for these assumptions, plus any separate regions excluded from Linux. A board sold with 1 GiB of installed RAM cannot meet that estimate before reserves are considered. The next available capacity is a candidate, subject to a run on the actual board and image.

Avoid using that 25% as a release rule. A long-lived appliance with customer extensions has a different uncertainty budget from a single-function controller with a frozen workload. Document what consumes the margin and how an update owner can tell when it is shrinking.

Account for update storage alongside memory. A rollback slot, the incoming image, staged files and a busy log partition can share the same device, creating I/O pressure when memory is reclaimed. Use the [SBC storage reliability](/posts/embedded-sbc-storage-reliability/) plan to define the aged or nearly full storage conditions for your test. Measure input or frame latency while those background operations are active.

## Test the overlap that sets the capacity

Run the UI and production data path at their maximum configured settings. Start an update with live acquisition still running if the product supports that workflow. Trigger application restart while buffered work remains. For a local kiosk, keep the representative web content, decoded video and physical peripherals in the run. Use the [embedded validation checklist](/posts/embedded-sbc-product-validation-checklist/) to track conditions across board variants.

Capture peaks, sustained pressure, OOM events, allocation failures, swap activity, and product timings. Keep a separate log of dropped frames or missed deadlines. Let the workload cycle long enough to observe growth, rotation of logs, and repeated reconnects. A recovery or maintenance job can be scheduled differently if peak overlap is the only reason to buy more RAM, but prove that revised behavior before quoting a smaller board.

Compare shortlisted variants using the same storage type, thermal setup and BSP, following the [real workload benchmark](/posts/edge-ai-hardware-benchmark-real-workload/) approach. Release the lower capacity only when representative units meet all latency limits, buffers initialize reliably, recovery works, and the observed low-water marks leave documented margin for the next software release.
