---
title: "Secure Device Identity and Factory Key Provisioning"
seo_title: "Secure Device Identity: Factory Key Provisioning Workflow"
description: "Build a secure factory provisioning workflow with per-device keys, authorized certificate issuance, retry-safe records, lifecycle controls, and release checks."
date: 2026-10-09
lastmod: 2026-10-27
draft: false
roadmap_id: "ESB-P021"
roadmap_status: "published"
author: "Embedded SBC Team"
schema_type: "BlogPosting"
keywords: ["secure device identity", "factory key provisioning", "IoT certificate provisioning", "embedded device PKI", "secure element provisioning"]
cover:
  image: "/images/posts/secure-device-identity-factory-provisioning-hero.jpg"
  alt: "Illustrative factory provisioning station with an SBC in a pogo-pin fixture and a workstation security token"
  caption: "AI-generated engineering illustration; no real credentials or customer manufacturing data are shown."
images: ["/images/posts/secure-device-identity-factory-provisioning-hero.jpg"]
---

Device identity provisioning is not complete when a certificate has been copied to a filesystem. The product must hold the matching private key, prove it can use that key, bind the identity to the correct physical unit, and recover predictably when the factory loses power halfway through the operation.

The manufacturing system also needs to know which identities were issued, which units passed, and which credentials must never be accepted. Without that record, rework can create duplicate devices, orphaned certificates, or units that pass the fixture but cannot connect after shipment.

This source-based design guide extends our [embedded firmware security resources](/embedded-firmware-bsp/). The workflow below is a reference architecture to adapt through a product threat model, not a claim of certification or a turnkey security guarantee.

## Separate Identity, Firmware Trust, and Authorization

Three concepts commonly get mixed together:

| Item | What it answers | Typical ownership |
|---|---|---|
| Device identity key | Which device possesses this credential? | Per-device protected key storage |
| Firmware signing key | Who may authorize executable firmware? | Restricted release-signing infrastructure |
| Service authorization | What is this device allowed to access? | Backend policy and lifecycle records |

Do not reuse one shared private key across a fleet. Do not place the firmware-signing private key on a production fixture merely because the fixture installs firmware. The device usually needs a public verification trust anchor for that purpose, not the signing secret. Keep the two workflows coordinated through the [secure boot key-management plan](/posts/secure-boot-key-management-embedded-products/).

A valid certificate alone should not authorize every backend operation. The service must also evaluate device state, tenant ownership, permissions, and revocation or disablement policy. A scrapped unit may retain a cryptographically valid key while being permanently unauthorized.

## Decide Where the Private Key Is Born

Generating the identity key inside protected device hardware can avoid transporting its plaintext through the factory computer. For example, Microchip's [ATECC608B cryptographic-operation documentation](https://onlinedocs.microchip.com/oxy/GUID-5C346303-A9CB-4341-B67F-4354AB92FE04-en-US-3/GUID-404D6C76-495B-4F8F-B534-2590E9C3102C.html) describes internal private-key generation with the associated public key returned. Actual capabilities and lifecycle restrictions depend on the selected part and configuration.

Alternatives include a TPM, another secure element, a suitable hardware-backed execution environment, or controlled injection when the architecture requires it. Evaluate algorithm support, available key slots, access policy, host-driver integration, manufacturing setup, rotation, and recovery. “Hardware-backed” is a starting point for review, not a complete threat model.

If software uses PKCS #11, the [OASIS base specification](https://docs.oasis-open.org/pkcs11/pkcs11-base/v3.0/os/pkcs11-base-v3.0-os.html) defines attributes such as sensitivity and extractability. Confirm how the actual token implements and enforces those attributes. An API flag by itself does not establish physical tamper resistance or protect a compromised host from requesting allowed signing operations.

## Make Provisioning a State Machine

Use durable, explicit states rather than a script that restarts from the beginning after any error. A practical sequence is:

1. Authenticate the station and authorize the work order.
2. Identify the board and verify approved hardware/firmware configuration.
3. Create or locate the device key under the intended access policy.
4. Request a certificate bound to the public key and authorized serial number.
5. Install the public certificate chain and verify a fresh challenge response.
6. Apply approved irreversible settings in the vendor-required order.
7. Reboot, recheck key use and policy, and record the release decision.

Some secure elements require configuration locking before key creation or certificate installation. Adapt the sequence to their documented lifecycle; never treat the list as a universal programming recipe. Validate irreversible steps on development units before enabling them on the production line.

{{< figure src="/images/posts/secure-device-identity-factory-provisioning-workflow.svg" link="/images/posts/secure-device-identity-factory-provisioning-workflow.svg" alt="Provisioning state machine linking authorized station, device key, certificate service, reboot verification, and release record" caption="A durable record binds the physical unit, public key, certificate, station, and final release decision." >}}

## A Signed Request Is Not Permission to Issue

A certificate signing request can demonstrate possession of its private key, but that does not prove the requester is an authorized production unit. The enrollment service must check the work order, expected serial range, product configuration, station credentials, quotas, and duplicate issuance policy.

Use an authenticated, reviewed enrollment protocol rather than inventing a transport. [RFC 7030](https://www.rfc-editor.org/rfc/rfc7030) defines Enrollment over Secure Transport; an implementation must also account for applicable updates and current transport-security requirements. The protocol does not replace the manufacturer's authorization rules.

Give station credentials limited permissions and a managed lifetime. Separate test and production issuing authorities. A developer fixture should not be able to mint a production identity simply by changing an environment variable. Restrict who can approve rework, replace an identity, or release a quarantined serial number.

## Design Retries Before the First Network Outage

Consider a common failure: the certificate service issues a certificate, but the station loses connectivity before receiving the response. Retrying as a new device can produce conflicting records. Use an idempotent transaction identifier and bind it to the device public-key fingerprint and manufacturing identity.

On resume, reconcile the server record with the actual device state. Do not generate a new key automatically when an earlier key may already have an issued certificate. If the two sides cannot be reconciled safely, quarantine the unit for a controlled recovery path.

| Interrupted point | Safe recovery objective | What must not happen |
|---|---|---|
| After key creation | Reuse or explicitly retire the recorded key | Silent replacement with no audit trail |
| After certificate issuance | Retrieve the same authorized result | Unbounded duplicate issuance |
| During certificate installation | Validate content and retry safely | Release with a partial or mismatched chain |
| During policy locking | Inspect actual lifecycle state | Blind repetition of irreversible commands |
| After final verification | Reconcile release record | Duplicate shipment identity |

The factory database should retain serial number, hardware revision, public-key fingerprint, certificate identifier, issuance transaction, station identity, software revisions, timestamps, and disposition. Never include private keys, bootstrap secrets, PINs, or reusable authentication tokens in logs. Public identifiers may still be commercially sensitive; apply access and retention controls.

## Test the Identity After the Device Reboots

A fixture-side signature check is necessary but insufficient. Verify that the production application can locate the intended key, present the correct certificate chain, authenticate to an authorized test endpoint, and reject an unauthorized endpoint. Confirm the behavior with invalid time, unavailable network, and expired or rejected credentials according to the product policy.

Test duplicate serial numbers, certificate/key mismatch, unauthorized station access, repeated transactions, and loss of power at defined safe interruption points. Confirm that a failed identity test blocks shipment without erasing the evidence needed for rework. Integrate these checks into the [factory flashing process](/posts/factory-flashing-workflow-embedded-linux/) and the [embedded hardware production gate](/posts/evt-dvt-pvt-embedded-hardware/).

## Plan Rotation, RMA, and Decommissioning

Identity is a lifecycle, not a one-time factory event. Decide whether certificates can renew while the key remains, whether a key can rotate, how ownership transfers, and what happens when protected storage fails. Make backend authorization changes independent enough to disable a stolen or scrapped unit promptly.

An RMA replacement board usually needs a new cryptographic identity with an explicit relationship to the service record it replaces. Copying the old private key onto a replacement board can undermine the original isolation assumptions and leave two working copies.

Before production release, demonstrate issuance, retry, reboot verification, renewal, disablement, and controlled replacement. A provisioning process is ready when both its success path and its failure paths leave an unambiguous, auditable state.
