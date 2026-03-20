# Troubleshoot Okta MFA failures after a password reset

| About this article | |
|---|---|
| **Article type** | Troubleshooting |
| **Audience** | IT administrators and end users |
| **Product** | Okta Workforce Identity |

After resetting a password in Okta, some users find that their multi-factor authentication (MFA) prompt does not appear, fails to send, or returns an error. This article helps you identify the cause and restore MFA functionality.

---

## Symptoms

- MFA prompt does not appear after password reset
- Authenticator app shows an invalid or expired code
- Push notification is not received after password reset
- User receives the error: `Your session has expired. Please sign in again.`
- User is stuck in a loop between the password reset and MFA screens
- SMS or email factor is sent to an old or unrecognized number or address

---

## Common causes

| Cause | Description |
|---|---|
| **Factor enrollment reset** | The password reset flow was configured to reset all enrolled factors, requiring re-enrollment |
| **Authenticator app out of sync** | The time on the user's device is out of sync, causing TOTP codes to be invalid |
| **Session conflict** | An active session from before the password reset is conflicting with the new session |
| **Policy misconfiguration** | The MFA policy does not apply correctly after a password reset trigger |
| **Factor not enrolled** | The user never completed factor enrollment or their enrollment was removed by an admin |
| **Network or email delay** | SMS or email factors are delayed due to carrier or mail server issues |

---

## Before you begin

Confirm the following before working through the resolution steps:

| Check | Details |
|---|---|
| **Admin access** | You need Okta admin access to view and modify user factor enrollment |
| **User identity confirmed** | Verify the user's identity through an alternative channel before making changes |
| **Okta System Log access** | You will need to review the System Log at **Reports > System Log** to identify the root cause |

---

## Resolution steps

Work through the steps below in order. After completing all steps, see [Verify resolution](#verify-resolution).

### Step 1: Check the Okta System Log for errors

The System Log is the fastest way to identify what happened during the password reset flow.

1. In the Okta Admin Console, go to **Reports > System Log**.
2. Search for the user's email address.
3. Filter events to the time of the password reset.
4. Look for any of the following event types:

    | Event type | What it indicates |
    |---|---|
    | `user.authentication.auth_via_mfa` | MFA was attempted — check the outcome for failure details |
    | `user.mfa.factor.deactivate` | A factor was deactivated during or after the reset |
    | `user.mfa.factor.reset_all` | All factors were reset — user must re-enroll |
    | `policy.evaluate_sign_on` | Sign-on policy was evaluated — check if MFA was required |

5. Note the event outcome and error message. Use this information to guide the remaining steps.

---

### Step 2: Check factor enrollment status

1. In the Okta Admin Console, go to **Directory > People**.
2. Search for and select the affected user.
3. Click the **Security** tab.
4. Under **Multifactor Authentication**, review the enrolled factors.

If no factors are enrolled, or all factors show as inactive, the user must re-enroll. See [Step 5: Re-enroll the user's MFA factor](#step-5-re-enroll-the-users-mfa-factor).

If factors are enrolled but not working, continue to Step 3.

---

### Step 3: Check the user's authenticator app time sync

TOTP-based authenticators (such as Okta Verify and Google Authenticator) generate codes based on the current time. If the device clock is out of sync by more than 30 seconds, codes will be invalid.

Ask the user to check their device time settings:

=== "iOS"
    1. Go to **Settings > General > Date and Time**.
    2. Enable **Set Automatically**.
    3. Restart the authenticator app and try again.

=== "Android"
    1. Go to **Settings > General Management > Date and Time**.
    2. Enable **Automatic date and time**.
    3. Restart the authenticator app and try again.

If the user is using Google Authenticator specifically, they can sync the time manually:

1. Open Google Authenticator.
2. Go to **Settings > Time correction for codes > Sync now**.

---

### Step 4: Clear the user's active sessions

A session conflict from before the password reset can prevent MFA from completing correctly.

1. In the Okta Admin Console, go to **Directory > People**.
2. Search for and select the affected user.
3. Click **More Actions > Clear User Sessions**.
4. Ask the user to close all browser windows and sign in again from a fresh session.

!!! note
    Clearing sessions signs the user out of all active Okta sessions across all devices. Inform the user before doing this.

---

### Step 5: Re-enroll the user's MFA factor

If the user's factors were reset or are not functioning, reset them so the user can re-enroll.

1. In the Okta Admin Console, go to **Directory > People**.
2. Search for and select the affected user.
3. Click the **Security** tab.
4. Under **Multifactor Authentication**, click **Reset** next to the affected factor, or click **Reset all factors** if multiple factors are affected.
5. Ask the user to sign in. They will be prompted to enroll their factor again from scratch.

!!! warning
    Resetting all factors requires the user to re-enroll every factor. Confirm this is necessary before proceeding, and verify the user's identity through an alternative method first.

---

### Step 6: Review the MFA policy

If the issue affects multiple users or recurs after re-enrollment, the MFA policy may be misconfigured.

1. In the Okta Admin Console, go to **Security > Multifactor**.
2. Review the **Factor Enrollment** policy assigned to the user's group.
3. Confirm that the policy requires MFA enrollment and does not have a grace period that allows users to skip MFA after a password reset.
4. Go to **Security > Authentication Policies** and review the sign-on rules applied to the user's applications.
5. Confirm that MFA is required at the correct step in the authentication flow.

!!! tip
    If users are consistently bypassing MFA after a password reset, check whether your **Password Policy** is configured to reset all factors on password change. Go to **Security > Authentication > Password** and review the **Reset all factors on password change** setting.

---

## Verify resolution

After completing the relevant steps above:

1. Ask the user to sign out completely and clear their browser cache.
2. Have the user navigate to your Okta sign-in page and enter their credentials.
3. Confirm that the MFA prompt appears and completes successfully.
4. Check **Reports > System Log** and confirm there are no new authentication errors for the user.

If the issue continues after completing all steps, collect the relevant System Log entries and contact [Okta Support](https://support.okta.com).

---

## Prevention and best practices

**Audit your password policy reset settings regularly.**
The **Reset all factors on password change** setting in your password policy can cause MFA disruptions if enabled unintentionally. Review this setting quarterly and confirm it aligns with your security requirements.

**Communicate factor re-enrollment to users proactively.**
If a password reset is expected to reset MFA factors, notify users in advance so they are not caught off guard. Include re-enrollment instructions in the notification.

**Enable Okta ThreatInsight.**
ThreatInsight detects suspicious authentication patterns and can alert you to brute-force attempts that may trigger unexpected password resets. Enable it at **Security > General > ThreatInsight**.

**Use phishing-resistant factors where possible.**
FIDO2/WebAuthn factors such as Okta FastPass are not affected by time-sync issues and are more resilient than TOTP-based authenticators. Consider rolling these out to high-risk user groups.

**Set up authenticator recovery options.**
Ensure users have a backup factor enrolled (such as email or a recovery phone number) so they can recover access without admin intervention if their primary factor fails.

---

## Related commands and endpoints

| Task | Method | Details |
|---|---|---|
| List user factors | Okta API | `GET /api/v1/users/{userId}/factors` |
| Reset a specific factor | Okta API | `DELETE /api/v1/users/{userId}/factors/{factorId}` |
| Reset all user factors | Okta API | `POST /api/v1/users/{userId}/lifecycle/reset_factors` |
| Clear user sessions | Okta API | `DELETE /api/v1/users/{userId}/sessions` |
| View System Log | Admin Console | **Reports > System Log** |
| Review authentication policy | Admin Console | **Security > Authentication Policies** |
| Review factor enrollment policy | Admin Console | **Security > Multifactor > Factor Enrollment** |

---

## Related articles

- [Enroll and manage MFA factors in Okta](https://help.okta.com)
- [Okta Authentication Policies overview](https://help.okta.com)
- [Configure password policies in Okta](https://help.okta.com)
- [Okta System Log event types reference](https://developer.okta.com/docs/reference/api/system-log/)