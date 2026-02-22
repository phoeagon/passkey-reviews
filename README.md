# Passkey Reviews

> Personal observations and technical commentary on passkey
> implementations across major platforms and financial institutions.\
> This document reflects my individual testing and experience. It does
> not guarantee completeness or accuracy, and implementations may change
> over time.

Passkey standards and vendor implementations evolve rapidly. By the time
you read this, certain behaviors described here may have changed.

I am a long‑time security enthusiast. I purchased Plug‑up FIDO U2F keys
when they were first introduced in Google's 2014 announcement. However,
I am not a security professional, and this document should not be
interpreted as formal security guidance.

------------------------------------------------------------------------

## Scope

This repository focuses primarily on **passkey login and enrollment
experience on websites and services**.

Some vendors discussed here also provide password manager
implementations (e.g., Apple Keychain, Chrome Password Manager). Those
components are not the focus of this document unless directly relevant
to the login flow.

For a broader and community‑maintained list of passkey‑enabled websites,
see the passkey directory repository:
https://github.com/phoeagon/passkeys

This document intentionally prioritizes notable behaviors, edge cases,
and interoperability observations rather than completeness.

------------------------------------------------------------------------

# Notable IT Giants

## Google

**Overall assessment:** Mature and well‑implemented.

-   Supports 10+ USB security keys and software passkeys.
-   Supports both hardware (USB) and software‑based credentials.
-   Allows naming and renaming during and after enrollment.
-   Allows disabling non‑FIDO 2FA methods.
-   First‑generation FIDO U2F (CTAP 1.0) keys continue to function as
    2FA devices.

### Observed Issue

As an early FIDO U2F adopter, I find the distinction between "2FA‑only"
and passwordless credentials unclear. Some keys are explicitly marked as
2FA‑only, while others are not---yet behave similarly.

If credential behavior appears inconsistent, deleting and re‑enrolling
often resolves classification ambiguity.

------------------------------------------------------------------------

## Apple

**Overall assessment:** Technically strong, but ecosystem‑restricted.

Apple supports both: - USB hardware security keys - Software‑based
passkeys (iCloud Keychain)

### Hardware Key Mode

When enabling USB hardware keys:

-   All other account recovery and 2FA options must be disabled:
    -   Trusted device codes
    -   Recovery contacts
    -   Recovery keys
-   Minimum: 2 hardware keys
-   Maximum: 6 hardware keys
-   Enrollment requires an active trusted Apple device (iPhone, iPad, or
    Mac).
-   Secondary Apple Mail logins do not qualify for enrollment.
-   After enrollment, iCloud login device changes are allowed.

### Software Passkeys

Modern Apple devices automatically create passkeys for Apple ID usage.

-   Seamless experience across Apple devices.
-   Supports cross‑platform login via QR workflow (e.g., Windows
    browser + iPhone).
-   No explicit enrollment flow for exporting to other password
    managers.
-   No native workflow to create a passkey outside of Apple's ecosystem.

**Implication:** Without disabling recovery methods, your only passkey
option is via Apple hardware.

------------------------------------------------------------------------

## Microsoft

**Overall assessment:** Strong implementation with modern constraints.

-   Supports multiple keys (tested beyond five).
-   Allows naming and renaming.
-   Does not force disabling other 2FA methods.
-   Non‑FIDO 2FA methods can be manually disabled.

### Limitations

-   Does **not** support first‑generation FIDO U2F (CTAP 1.0).
-   Requires CTAP 2.0 capable devices.
-   Non‑resident keys are not supported, even if CTAP 2.0 capable.

This may impact enterprise hardware deployments.

------------------------------------------------------------------------

## Meta (Facebook / WhatsApp)

**Overall assessment:** Inconsistent and limited.

As of February 2026:

-   Only Facebook and WhatsApp support passkeys.
-   Instagram and Threads do not.
-   Enrollment only available via mobile app.
-   Maximum: 1 passkey.

The enrolled passkey may be software‑synced (e.g., Chrome or iCloud).
However, the single‑credential limit is restrictive.

------------------------------------------------------------------------

## Twitter

Functional in testing.\
Not tested as extensively as Google or Apple due to lower account
criticality.

------------------------------------------------------------------------

# Software Development Platforms

## GitHub

**Overall assessment:** Solid implementation.

Supports: - Chrome Password Manager passkeys - iCloud Keychain
passkeys - First‑generation FIDO U2F - Resident keys

While not stress‑tested extensively, interoperability appears strong.

------------------------------------------------------------------------

# Banks and Brokerages

Financial institutions generally lag behind major technology companies
in passkey maturity, though progress is visible.

------------------------------------------------------------------------

## Vanguard

-   Supports USB and synced passkeys.
-   Maximum enrollment: approximately 4--6 keys.
-   Previously restricted by AAGUID (e.g., YubiKey allowlisting); no
    longer observed as of February 2026.
-   Non‑FIDO 2FA can only be disabled if at least two passkeys are
    enrolled.
-   Employment‑based plans (e.g., 401k) do not allow disabling non‑FIDO
    2FA.

------------------------------------------------------------------------

## Bank of America

**Assessment:** Limited and inconsistent.

-   Maximum: 2 security keys.
-   Enrollment workflow inconsistencies across browsers.
-   Software‑based passkeys appear blocked.

Notably, Merrill (its brokerage branch) behaves differently.

------------------------------------------------------------------------

## Merrill Lynch

-   Independent enrollment from Bank of America.
-   Supports software‑synced passkeys.
-   Keys must be re‑enrolled even if already configured for Bank of
    America.

------------------------------------------------------------------------

## Capital One

-   Supports multiple passkeys.
-   Supports hardware and software credentials.
-   Unclear whether non‑FIDO 2FA can be disabled.

------------------------------------------------------------------------

## Wells Fargo

-   Supports multiple passkeys.
-   Supports hardware and software credentials.
-   Unclear whether non‑FIDO 2FA can be disabled.

------------------------------------------------------------------------

## U.S. Bank

Passkey rollout appears partial.

-   Some browser/device combinations show passkey login.
-   No enrollment option available in testing.
-   Support escalation did not enable access.

------------------------------------------------------------------------

## Chase

Reportedly allows enrollment during password reset workflows.\
Not independently verified.

------------------------------------------------------------------------

## Robinhood

-   Enrollment only via iOS app.
-   Maximum: 1 passkey.
-   Supports software‑synced credentials (commonly iCloud Keychain).

------------------------------------------------------------------------

# Retailers

Passkey support among retailers varies but is increasing.

------------------------------------------------------------------------

## CVS

-   Maximum: 1 passkey.
-   Enrollment via mobile app only.

------------------------------------------------------------------------

## Target

-   Supports multiple passkeys.
-   Supports browser and app enrollment.
-   Allows renaming.
-   Strong implementation.

------------------------------------------------------------------------

## Walmart

-   Supports multiple passkeys.
-   Supports browser and app enrollment.
-   Allows renaming.
-   Strong implementation.

------------------------------------------------------------------------

## Amazon

-   Supports multiple passkeys.
-   Allows renaming.
-   Mature implementation.

------------------------------------------------------------------------

## AliExpress

-   Supports passkeys.
-   Notably, Taobao (China‑focused platform) does not.

------------------------------------------------------------------------

# Contribution Guidelines

This document is intentionally observational and experience‑driven.

If you have corrections, updates, or additional interoperability
findings, please open a pull request.

Technical rigor and reproducibility notes are especially welcome.
