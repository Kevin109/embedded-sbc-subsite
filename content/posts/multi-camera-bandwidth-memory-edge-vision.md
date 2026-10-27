---
title: "Multi-Camera Bandwidth and Memory Planning for Edge Vision"
seo_title: "Multi-Camera Bandwidth and Memory Budget for Edge Vision"
description: "Calculate multi-camera link bandwidth, DDR traffic, and frame-buffer memory with a four-camera example, DMA-BUF checks, and a sustained-load test plan."
date: 2026-10-20
lastmod: 2026-10-27
draft: false
roadmap_id: "ESB-P023"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["multi-camera bandwidth", "edge vision memory bandwidth", "camera frame buffer memory", "MIPI CSI bandwidth calculation", "DMA-BUF zero copy"]
cover:
  image: "/images/posts/multi-camera-bandwidth-memory-edge-vision-hero.jpg"
  alt: "Illustrative four-camera inspection rig connected to an embedded vision carrier board"
  caption: "AI-generated engineering illustration; the calculations below are analytical examples, not measurements of this scene."
images: ["/images/posts/multi-camera-bandwidth-memory-edge-vision-hero.jpg"]
---

Four cameras can fit the advertised input count of an SoC and still overwhelm the product. The limit may be a shared CSI receiver, the ISP, a format conversion, DDR traffic, or a queue that grows faster than the application consumes frames. Counting connectors and accelerator TOPS misses most of that path.

Build three budgets separately: bandwidth entering the device, traffic moving through memory, and memory occupied by buffers. They answer different questions. A system can have enough RAM capacity but insufficient sustainable memory bandwidth, or enough average bandwidth but too little queue headroom for a burst.

This guide expands our [edge AI vision system engineering](/edge-ai-computing/) coverage. The worked example is an explicit calculation, not a hardware benchmark or a compatibility claim for any specific processor.

## Start With a Per-Camera Stream Contract

For each camera, record active resolution, frame rate, wire format, host-memory format, exposure behavior, triggering, metadata, and allowed frame loss. Then record every consumer: ISP, inference preprocessing, encoder, display, recorder, and diagnostics.

Ask the board supplier how camera ports map to receivers, virtual channels, ISP contexts, and memory paths. Four visible connectors do not establish four independent full-rate pipelines. Check the selected sensor modes and BSP drivers as well as the silicon capability.

Our [MIPI CSI and USB camera selection guide](/posts/mipi-csi-vs-usb-camera-embedded-vision/) helps choose the transport. Once chosen, budget its actual topology: USB devices may share a host controller, and CSI streams may share receiver or processing resources.

## Calculate Payload Before Adding Transport Margin

For uncompressed active pixels:

```text
payload bytes/second = width × height × frames/second × bits/pixel ÷ 8
```

Assume four cameras, each producing 1920 × 1080 at 30 frames/s, with packed RAW10 active-pixel payload. Each camera contributes 77,760,000 bytes/s, or 77.76 MB/s in decimal units. Four contribute 311.04 MB/s, equivalent to about 2.488 Gbit/s of active-pixel payload in aggregate.

This is not the required lane-rate setting. Packet overhead, sensor timing, embedded metadata, burst behavior, and the distribution of streams across links still matter. Derive the link configuration from the sensor mode and receiver documentation. Do not distribute aggregate traffic evenly over lanes that are not actually shared.

For USB cameras using compression, calculate or measure the worst relevant encoded stream rather than assuming the average bitrate is a safe limit. Noise, motion, and scene detail can change traffic. Reserve bandwidth for control and other devices according to the actual host topology.

## Wire Format Is Not Memory Format

A RAW10 stream may be unpacked into 16-bit storage after capture. An ISP may then emit NV12, while a model preprocessing stage creates RGB or an accelerator-specific tensor. Each representation changes both storage and traffic.

For this same 1080p30 example, ignoring padding and metadata:

| Representation | Bytes per pixel | One camera, MB/s | Four cameras, MB/s |
|---|---:|---:|---:|
| Packed RAW10 | 1.25 | 77.760 | 311.040 |
| RAW stored in 16-bit words | 2 | 124.416 | 497.664 |
| NV12, 8-bit 4:2:0 | 1.5 | 93.312 | 373.248 |
| RGB888 | 3 | 186.624 | 746.496 |

The [V4L2 pixel-format documentation](https://docs.kernel.org/userspace-api/media/v4l/pixfmt-v4l2.html) explains that line stride and image size can include layout requirements beyond active pixels. Budget using the format and buffer sizes returned by the driver. For multi-planar formats, account for every plane and its alignment; do not allocate from width × height alone.

## Count Every Full-Frame Read and Write

Suppose the illustrative pipeline stores unpacked RAW16 in DDR, the ISP reads it and writes NV12, and inference preprocessing plus an encoder each read the full NV12 frame. Ignoring all additional traffic:

| DDR transaction across four cameras | Traffic, MB/s |
|---|---:|
| Capture writes RAW16 | 497.664 |
| ISP reads RAW16 | 497.664 |
| ISP writes NV12 | 373.248 |
| Inference preprocessing reads NV12 | 373.248 |
| Encoder reads NV12 | 373.248 |
| Analytical subtotal | 2,115.072 |

That is approximately 2.115 GB/s before model tensors, encoder reference frames, display reads, CPU access, padding, or other system work. Some hardware can stream directly between stages and avoid the RAW round trip; other pipelines add copies. Replace the assumptions with the actual data path.

{{< figure src="/images/posts/multi-camera-bandwidth-memory-edge-vision-workflow.svg" link="/images/posts/multi-camera-bandwidth-memory-edge-vision-workflow.svg" alt="Four-camera memory traffic example showing RAW16 capture write and ISP read, followed by NV12 writes and two consumer reads" caption="A 311 MB/s wire payload can produce a much larger DDR workload. Values are calculated examples, not measured throughput." >}}

Compare the completed budget with sustainable bandwidth under the intended workload, not the memory chip's peak transfer specification. Arbitration, access patterns, thermal throttling, and competing masters affect the usable result. A generous-looking peak number is not a pass criterion.

## Size Buffers Without Hiding Latency

One tightly packed 1080p NV12 frame occupies 3,110,400 bytes. Six buffers per camera across four cameras occupy 74,649,600 bytes, about 71.2 MiB. Four RAW16 buffers per camera add 66,355,200 bytes, about 63.3 MiB. Together those two illustrative pools consume about 134.5 MiB before alignment, application memory, inference workspaces, or encoder surfaces.

Count unique allocations, not every reference to a shared buffer. Conversely, do not assume that two stages share storage merely because the software calls the path “zero copy.” Inspect actual allocations and format conversions.

At 30 frames/s, one frame interval is about 33.3 ms. Several queued old frames can make the display look smooth while decisions become stale. Define whether the product should preserve every frame, drop the oldest frame, or reject new frames under overload. Bound queue depth and measure capture-to-decision age.

Synchronized cameras can create aligned bursts even when the average traffic looks acceptable. Test the intended trigger pattern and exposure modes, not just four independently running cameras whose transfers happen to spread out.

## Zero Copy Still Needs Compatibility and Synchronization

The [V4L2 DMA-BUF interface](https://docs.kernel.org/userspace-api/media/v4l/dmabuf.html) allows supported drivers to exchange buffers through file descriptors. Sharing requires compatible exporters, importers, formats, and layouts. A downstream engine that cannot consume the captured format may still need a conversion or copy.

Buffer ownership also matters. The [kernel DMA-BUF synchronization documentation](https://docs.kernel.org/driver-api/dma-buf.html) describes the mechanisms for coordinating asynchronous access. Do not let capture overwrite a buffer that an encoder or accelerator still reads. A zero-copy design with incorrect synchronization can produce rare corrupted frames that disappear during slow debugging.

Even a correct shared buffer is written and read by hardware. “Zero copy” removes particular redundant copies; it does not mean zero DDR traffic.

## Validate in Layers, Then Run Everything Together

Bring up one camera and confirm timestamps, pixel format, stride, and frame integrity. Add cameras one at a time, then add ISP processing, inference, recording, display, and diagnostics. At each step, record drops, queue occupancy, memory use, CPU load, temperatures, and end-to-end latency.

Finally run the complete workload in the enclosure with realistic storage and networking. Exercise high-detail scenes, synchronized bursts, camera reconnects where supported, and a slow downstream consumer. Confirm bounded recovery instead of steadily increasing memory use.

Use the [camera pipeline integration checklist](/posts/camera-pipeline-edge-ai-vision/) and [real-workload AI benchmark plan](/posts/edge-ai-hardware-benchmark-real-workload/) to preserve the evidence. If inference precision is changed through [INT8 model calibration](/posts/int8-quantization-edge-ai/), rerun the system test: faster inference can shift the bottleneck to preprocessing or memory rather than eliminate it.

The final design record should contain the stream contract, transport mapping, transaction budget, unique buffer inventory, overload policy, and sustained test results. Those artifacts are more useful than a single “four cameras supported” line in a specification.
