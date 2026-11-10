# Microsoft Entra ID Sign In Investigations

> **My perspective:** During my internship I investigated Microsoft Entra ID sign in events. This section shares what I learned about reading them. It isn't a complete reference, and Microsoft's documentation is always the final word.

## The golden rule

**Never decide an event is malicious based on an error code alone.** An error code tells you *what* happened during sign in, not *why* or *who*. Always combine it with the failure reason, IP reputation, device details, authentication details, and surrounding activity.

## What to investigate

| What to check | Why it matters |
|---|---|
| **Failure reason** | Explains why authentication failed. More specific than the code |
| **Additional details** | Extra context about the failure |
| **IP reputation** | Shows if the source is a proxy, VPN, or known bad infrastructure |
| **Device status** | Whether the device is managed and compliant |
| **Authentication details** | Whether a password, MFA, passkey, or existing token was used |
| **Conditional Access** | Which policies allowed or blocked the sign in |
| **Application** | Which app the user was trying to reach |
| **Sign in history** | Whether this matches the user's normal behavior |
| **Was it successful?** | Whether access was actually gained. A failure stopped at the door. A success means someone got in |

## Example: why error code 50053 needs a second look

Error 50053 can mean two very different things:

| Cause | What it suggests |
|---|---|
| Account locked after too many failed sign ins | Possible password guessing, or a user who forgot their password |
| Sign in blocked because the IP has malicious activity | The source itself is known bad |

Same code, different stories. The **Failure reason** field tells you which one you're looking at.

## Authentication messages you may see

These appear in the sign in log's authentication details. They aren't all errors. Some just describe how authentication was handled. Exact wording can vary.

| Message | Meaning | How to read it |
|---|---|---|
| MFA required | Extra authentication was needed | Check whether the user completed it |
| MFA satisfied by claim in token | An existing token already proved MFA | A new prompt may not have been needed. Not a bypass by itself |
| MFA not required | No MFA was needed for this event | Check which Conditional Access policies applied |
| MFA not required during Windows broker logon flow | The Windows sign in process didn't need a separate MFA prompt | Often an existing session or token. Not automatically a bypass |
| Password in cloud / Correct password | The password was checked and was correct | A correct password doesn't mean sign in fully succeeded. Check the overall result |
| Passkey assertion not acquired | The passkey step didn't complete | User may have cancelled, skipped, or hit a device issue |

## IP reputation tags

IP reputation tools label IPs with tags. These are **not** Entra error codes. They come from whichever enrichment tool your team uses, and names differ between tools.

| Example tag | Meaning | How to read it |
|---|---|---|
| PROXY | IP linked to proxy infrastructure | Traffic may be routed through a middleman, hiding the real source |
| CALLBACK_PROXY | IP linked to callback proxy infrastructure | Possible proxy based routing. Check the source further |
| AD_FRAUD | IP linked to advertising fraud | Shady traffic, but not proof of account compromise |
| anonymous: false | Not flagged as anonymous | Doesn't prove the IP is safe or residential |

## Files in this folder

| File | What it is |
|---|---|
| [Error Codes](error-codes.md) | Common sign in error codes, checked against Microsoft's documentation |


## References

- [Microsoft Entra authentication and authorization error codes](https://learn.microsoft.com/en-us/entra/identity-platform/reference-error-codes)
- [How to troubleshoot Microsoft Entra sign in errors](https://learn.microsoft.com/en-us/entra/identity/monitoring-health/howto-troubleshoot-sign-in-errors)
- [Troubleshoot sign in problems with Conditional Access](https://github.com/MicrosoftDocs/entra-docs/blob/main/docs/identity/conditional-access/troubleshoot-conditional-access.md)
