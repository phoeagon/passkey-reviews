# Software-Synced Passkeys --- Deep Technical Review

> A field analysis of software-backed WebAuthn credentials across Apple,
> Google, and Bitwarden ecosystems, with operational guidance for
> resilience and portability.

This document analyzes real-world passkey implementations across major
platforms.\
It focuses on synchronization behavior, downgrade scenarios, attestation
constraints, counter semantics, and vendor enforcement patterns.

This is not a marketing comparison. It is an interoperability study.

-------------------------------------------------

# TL;DR:. Recommended Enrollment Strategy

If multiple credentials allowed:

Enroll:

-   Apple
-   Google
-   Bitwarden
-   Two hardware keys

Name them clearly.

If restricted:

-   Attempt all three ecosystems
-   Use QR workflow where possible
-   Use CXP propagation
-   Validate immediately

Do not assume synchronization works without testing.


------------------------------------------------------------------------

# 1. Executive Summary

There is currently **no vendor-neutral, durable, file-based backup
format** for passkeys that works across ecosystems.

The only semi-portable mechanism available today is:

-   **CXP (Credential Exchange Protocol)** --- currently exposed via iOS
    26 APIs.

For a resilient software-synced strategy, the most robust configuration
observed is:

-   macOS + Chrome (desktop enrollment surface)
-   iPhone (iOS 26+) for CXP mobility
-   Bitwarden (hosted) for archival/export capability
-   Google Password Manager for Windows/Android coverage
-   At least two hardware FIDO2 security keys

This is not redundancy by accident --- it is redundancy by necessity.

------------------------------------------------------------------------

# 2. Threat Model & Design Goals

When engineering a passkey strategy, assume:

1.  Vendors may restrict acceptable authenticators.
2.  Vendors may enforce attestation validation.
3.  Vendors may enforce signature counter semantics.
4.  Vendors may silently downgrade CTAP 2.0 to U2F.
5.  Ecosystems may fragment or deprecate export paths.

Design goals therefore include:

-   Cross-platform login survivability
-   Backup capability independent of Apple/Google
-   Hardware fallback
-   Resistance to ecosystem lock-in
-   Recovery under restrictive vendor policy

------------------------------------------------------------------------

# 3. FIDO Identification & Enforcement Mechanics

Authenticators are identifiable through:

-   AAGUID (self-declared identifier)
-   Attestation certificate chain
-   Transport (USB, NFC, BLE, internal)
-   Resident key capability flags
-   User verification flags
-   Signature counter behavior

### 3.1 AAGUID

AAGUID is self-declared.\
It can be spoofed in custom firmware.

However, spoofing AAGUID without valid attestation achieves little if
the RP verifies attestation certificates.

### 3.2 Attestation

To cryptographically attest as a commercial device, you must possess the
manufacturer's attestation private key.

Without it:

-   You may enroll only on sites that ignore attestation.
-   Strict RPs will reject enrollment.

Open-source FIDO firmware exists, but strict vendors rarely whitelist
them even if FIDO certified.

### 3.3 Signature Counter

FIDO supports a monotonically increasing counter.

Many software passkeys return:

    signCount = 0

This is compliant.

However, if an RP enforces high-watermark validation:

-   Synchronizing credentials across ecosystems will fail.
-   Cross-platform logins may become invalid.

This is one of the least discussed but most operationally significant
edge cases.

------------------------------------------------------------------------

# 4. Platform-Specific Behavior

## 4.1 Apple iCloud Keychain

### Observed Characteristics

-   Seamless sync across iOS/macOS
-   Biometric-bound local usage
-   Hidden Apple Account passkeys
-   Possible device-bound fallback behavior
-   No file-based export/import
-   CXP only on iOS 26

### Device Binding

In certain flows, iCloud appears to:

-   Generate device-bound credentials
-   Downgrade to CTAP 1.0 when requested

This may result in credentials that do not sync across Apple devices.

### Desktop Enrollment Advantage

macOS provides a QR-code-based cross-device flow.\
This allows:

-   Delegation to iPhone
-   Bypass of some vendor hardware restrictions
-   Forced storage into a chosen password manager

This workflow is operationally powerful.

------------------------------------------------------------------------

## 4.2 Google Password Manager

### Strengths

-   Synchronizes across Windows/macOS/Android/iOS/ChromeOS
-   Large ecosystem reach

### Downgrade Behavior

If a site requests U2F:

-   Chrome may create U2F credentials
-   These may not appear in Android UI
-   They may appear in iOS export interface

Android may also generate device-bound passkeys:

-   Not visible in Chrome password UI
-   Visible in Google Account security page

### Export Limitations

-   No JSON export
-   CXP required (iOS 26)
-   No documented durable archive path

This is a significant portability limitation.

------------------------------------------------------------------------

## 4.3 Bitwarden

### Strengths

-   Server-side sync
-   Supports CTAP 1.0 credentials
-   JSON export/import (Bitwarden format)
-   CXP (mobile client with feature flags)

Required flags:

-   cxp-import-mobile
-   cxp-export-mobile

Available on official hosted services.\
Not officially supported on most self-hosted deployments without custom
builds.

### Limitations

-   CLI lacks passkey support
-   JSON format not interoperable with other vendors
-   CXP requires mobile client support

### Strategic Value

Bitwarden is currently the only practical path for:

-   Long-term archival
-   Independent credential custody
-   Exportable recovery state

------------------------------------------------------------------------

# 5. Vendor Restriction Patterns

Some sites enforce:

-   Hardware-only enrollment
-   Specific hardware models
-   FIDO Level 2 certification
-   Enterprise-provisioned authenticators
-   Limited credential count

Operational response:

1.  Attempt enrollment on all ecosystems.
2.  Vendors frequently forget to block at least one.
3.  Use CXP to propagate credential outward.
4.  Validate on each ecosystem.
5.  Maintain at least two hardware keys.

If strict attestation enforced:

-   There is no bypass without private key compromise.
-   Hardware compliance becomes mandatory.

------------------------------------------------------------------------

# 6. CTAP Downgrade Reality

CTAP 2.0 does not allow explicit "U2F-only" requirement.

However, in practice:

-   Site may accept enrollment.
-   Then reject credential server-side.
-   Result: effective downgrade enforcement.

Specification compliance does not prevent restrictive policy.

------------------------------------------------------------------------

# 7. Long-Term Risk Outlook

Expect:

-   Ecosystem fragmentation
-   Export path instability
-   Silent behavior changes
-   Increased attestation enforcement

Passkeys improve usability --- but portability is not guaranteed by
design.

Resilience must be engineered intentionally.

------------------------------------------------------------------------

# 9. Final Assessment

Software-synced passkeys are usable today.\
They are not universally portable.

The most stable configuration combines:

-   Apple ecosystem stability
-   Google ecosystem coverage
-   Bitwarden archival capability
-   Hardware fallback

Without redundancy, lock-in risk is non-trivial.

Design for failure scenarios, not ideal flows.
