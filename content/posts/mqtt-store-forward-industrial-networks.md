---
title: "MQTT Store-and-Forward for Unreliable Industrial Networks"
seo_title: "MQTT Store-and-Forward for Industrial IoT Gateways"
description: "Build an MQTT store-and-forward queue for industrial gateways with outage sizing, durable commits, deduplication, replay pacing, and recovery tests."
date: 2026-11-24T09:00:00+08:00
lastmod: 2026-11-24T09:00:00+08:00
draft: false
roadmap_id: "ESB-P032"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["MQTT store and forward","MQTT offline buffering","industrial MQTT gateway","durable telemetry queue","MQTT replay deduplication"]
cover:
  image: "/images/posts/mqtt-store-forward-industrial-networks-hero.jpg"
  alt: "Rugged data-logging gateway and industrial cellular router connected in a test rack"
  caption: "AI-generated engineering illustration."
images: ["/images/posts/mqtt-store-forward-industrial-networks-hero.jpg"]
---

A gateway that reconnects to MQTT may still lose hours of data. Samples generated while there was no network need a local retention policy, and data accepted by a broker can still be missing from the destination database. Decide where a record becomes durable and what acknowledgment permits the gateway to discard it.

In an [industrial edge gateway](/industrial-embedded-computing/), keep acquisition independent of the WAN. The local collector writes an event to a durable queue. A publisher drains eligible events when connectivity returns. Acquisition can keep running through a forced disconnect, provided the device still has power and free queue capacity.

## Define the acknowledgment boundary

| State | Meaning | Release decision |
|---|---|---|
| Accepted by collector | The input reached application memory | Record can still be lost on reset |
| Committed to local queue | The local durability contract has been met | Eligible to publish |
| Acknowledged by MQTT peer | Protocol exchange reached the configured success condition | Remove only if broker handoff is the agreed boundary |
| Committed by destination | The consumer durably applied the event | Remove under an end-to-end confirmation contract |

The MQTT 5 specification describes the QoS flow, session state and expiry controls. A successful QoS 1 PUBACK concerns delivery to that MQTT peer. It carries no inherent proof that a downstream consumer committed a database row. Session expiry helps a session survive reconnects under the configured conditions. Local application storage handles records created offline and covers device restarts according to its own durability design. [OASIS MQTT 5.0 standard](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html).

{{< figure src="/images/posts/mqtt-store-forward-industrial-networks-workflow.svg" link="/images/posts/mqtt-store-forward-industrial-networks-workflow.svg" alt="Telemetry is committed to a durable queue, published to an MQTT broker and acknowledged after the chosen handoff" caption="Choose the acknowledgment boundary before implementing deletion and retries." >}}

## Size the queue and the recovery window

Assume 20 events/s, an average stored footprint of 250 bytes/event and a 12-hour WAN outage. The payload and record overhead together total `20 × 250 × 43,200` bytes, or 216 MB decimal. If your stress allowance is 50%, reserve 324 MB for records. Add separate allowances for database journal, indexes, temporary files and storage headroom where they are excluded. Measure storage growth with representative record formats and confirm endurance under your commit frequency.

Backlog drain depends on net replay capacity. At a sustained rate of 60 successful deliveries/s, 20 live events/s leave 40 events/s for backlog. The 864,000-event backlog from the example takes about six hours to clear. Use measured sustained delivery at the intended acknowledgment boundary. A connection that oscillates on and off can drain much more slowly than this simple estimate.

Define what happens when the queue fills. For telemetry, retaining recent samples with a recorded gap may suit a real-time dashboard. Alarms or audit records may require reserved space and an operator alert. Document which classes can be expired, aggregated or dropped, and make the decision visible through counters. Include the queue in your [embedded storage reliability](/posts/embedded-sbc-storage-reliability/) plan.

## Assign stable event IDs and preserve event time

Give each event an application ID that survives retries, such as a device identity plus a durable sequence number or generated unique identifier. Treat MQTT packet identifiers as protocol state. If delivery is retried after a device restart or a lost acknowledgment, use the same application ID. The receiver can apply a unique constraint or equivalent idempotent transaction tied to that ID.

Test the case where the receiver commits an event and the gateway loses power before recording the acknowledgment. On restart the record remains in the queue, so it is delivered again with the original ID. The receiver should acknowledge the durable result and preserve exactly one application record. Include deduplication retention in the design if the same event can be replayed after a long outage.

Record acquisition time, receipt time if relevant, and an indicator of clock quality. A gateway that starts without reliable wall time can still impose local order with persistent sequence numbers. Decide how the backend will handle stale samples alongside current ones. Use bounded replay batches and a live-traffic allocation so a week-old backlog cannot starve the current heartbeat.

## Test a power cut as well as a WAN cut

1. Disconnect the WAN while input continues. Measure event count, growth rate, oldest queued age and available space.
2. Remove power around a local commit and after a publish. Reboot and reconcile committed IDs against what the receiver applied.
3. Restart the broker and let a session expire in controlled runs. Verify application replay and deduplication still work.
4. Restore a constrained link. Measure net drain rate, new-data latency and recovery under another short outage.
5. Fill the queue with test data and confirm that the chosen retention policy reports what was discarded and preserves protected classes.

Record the acceptable loss window for sudden power failure and test it on the actual storage and filesystem. A storage engine's transaction return alone does not establish how that device handles loss of power. Review the storage stack, batching policy and fixture evidence together. Use [industrial field diagnostics](/posts/field-diagnostics-embedded-industrial-devices/) for the counters and service logs, and preserve measurement quality from the [OPC UA gateway](/posts/opc-ua-edge-gateway-architecture/) if it supplies the events.

The release evidence should show which events survive, how duplicates are handled and how long a full backlog takes to clear on the connection the product will actually use.
