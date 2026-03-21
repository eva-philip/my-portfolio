# Before & After: Release Notes

This sample demonstrates how I transform raw engineering notes into
user-focused release notes. The "before" reflects a real-world engineering
handoff. The "after" shows how I apply tech writing principles and style guide to  produce release notes that are clear, scannable, and useful to the people reading them.

!!! note "About this sample"
    This is a fictitious sample created to demonstrate my documentation 
    process and writing style. It does not represent real release notes 
    from any organization.
    
---

## Before: Engineering Dump

> *What the engineering team handed off for the Microsoft Entra ID June 2025 release.*

```
june release - entra ID

- conditional access now supports custom compliance partners, 
  not just intune. admins configure it under protection > 
  conditional access > compliance. we added 3 new partners to 
  the list (jamf, kandji, and ivanti). ticket PLAT-4491
- the sign-in logs export was broken for tenants with over 100k 
  logs, it was timing out. fixed by chunking the export into 
  batches of 25k. users won't notice anything different in the UI 
  but the download will take a bit longer for large tenants
- we're deprecating the ADAL library. apps using ADAL for 
  authentication will need to migrate to MSAL before september 
  30 2025 or they'll stop working. Kenji flagged that a lot of 
  enterprise customers are still on ADAL so this needs 
  to be communicated clearly
- added a new risky user report filter - admins can now filter 
  by risk level (low medium high) and by detection type. makes 
  it easier to triage. no breaking changes
- fixed UI bug where the MFA registration page would show a 
  blank screen on iOS 17 safari if the user had private relay 
  turned on
- group-based license assignment now shows an error message 
  instead of failing silently when a user doesn't have a 
  required prerequisite license. previously it just didn't 
  assign and gave no feedback
```

---

## After: User-Facing Release Notes

> *What I published to the documentation site.*

---

### Microsoft Entra ID - Release Notes

**June 2025**

---

#### What's New

**Conditional Access supports third-party compliance partners**

You can now use third-party device compliance partners with Conditional
Access policies, in addition to Microsoft Intune. The following partners
are available in this release:

- Jamf Pro
- Kandji
- Ivanti Neurons for MDM

To configure a compliance partner, go to **Protection** > **Conditional Access**
> **Compliance policies** in the Microsoft Entra admin center.

---

#### Improvements

**Risky Users report includes new filter options**

You can now filter the Risky Users report by risk level (Low, Medium, or
High) and by detection type. Use these filters to prioritize which users
you review and remediate first.

**Group-based license assignment shows errors for missing prerequisite licenses**

When a group-based license assignment fails because a user is missing a
required prerequisite license, Entra ID now displays a specific error
message. Previously, the assignment failed without any notification.

---

#### Bug Fixes

**Sign-in log exports fail for large tenants**

Resolved an issue where sign-in log exports timed out for tenants with
more than 100,000 log entries. Exports now process in batches. Exports
for large tenants may take slightly longer to complete.

**MFA registration page shows a blank screen on iOS 17 with iCloud Private Relay**

Resolved an issue where the MFA registration page displayed a blank screen
on Safari for iOS 17 when iCloud Private Relay was enabled. The page now
loads correctly.

---

#### Deprecations and Breaking Changes

!!! warning "Action Required - ADAL Library Deprecation"

    Microsoft is deprecating the Azure Active Directory Authentication
    Library (ADAL). Apps that use ADAL to authenticate will stop working
    after **September 30, 2025**.

    **What you need to do:** Migrate your apps from ADAL to the Microsoft
    Authentication Library (MSAL) before the deprecation date.

    For migration steps, see [Migrate applications to MSAL](#).

---

## What Changed and Why

| Issue in the "Before" | What I did in the "After" |
|---|---|
| Passive voice throughout ("was broken", "was configured by") | Rewrote all sentences in active voice ("you can now configure", "Entra ID now displays") |
| Past tense used for fixes and changes | Switched to present tense throughout to reflect current product state |
| Engineer names and ticket numbers included | Removed - not relevant to end users |
| Breaking change buried in the middle of the list | Moved to a dedicated "Deprecations and Breaking Changes" section with a warning admonition and a clear action step |
| Deprecation deadline written as "september 30 2025" | Formatted as **September 30, 2025** and bolded for visibility |
| "makes it easier to triage" - vague benefit statement | Rewritten as a direct second-person sentence: "Use these filters to prioritize which users you review and remediate first" |
| Silent failure described without user impact | Reframed around what the user now sees instead of what the system does internally |
| Informal language ("a bit longer", "flagged") | Replaced with precise, professional language consistent with the Microsoft Style Guide |