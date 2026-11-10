# Runbook: Impossible Travel / Atypical Travel

**Severity:** Varies by detection and context

**Data source:** Microsoft Entra ID Protection and Microsoft Defender for Cloud Apps

## Description

A user signed in from two places so far apart that they couldn't realistically have traveled between them in the time between sign ins. Either something is making their location look wrong, or two different people are using the same account.

## Background

Microsoft has two similar detections:

| Detection | Where it comes from | What it flags |
|---|---|---|
| **Atypical travel** | Microsoft Entra ID Protection | Two sign ins from distant locations, where at least one is unusual for the user |
| **Impossible travel** | Microsoft Defender for Cloud Apps (shown in Entra too) | Sign ins from locations too far apart to travel between in the time elapsed |

**Good to know:** Microsoft's detections learn each user's normal behavior first. New users have little history, so early alerts may be less reliable.

### Common benign causes

- **VPNs and proxies** make a user appear in another city or country
- **Mobile phones** switching networks, or syncing email after the user lands somewhere new
- **Real travel** where the user signed in before and after a flight
- **Cloud services** connecting on the user's behalf from distant data centers
- **Shared accounts** used by people in different locations (a bad practice, but it happens)

### Why it can be malicious

If an attacker steals a user's password or session, they sign in from **their** location while the real user is still signing in from **theirs**. One of the two sign ins is the real user. The other may be the attacker.

## Example finding (fictional)

All values are fictional. IPs are from ranges reserved for documentation.

| Field | Sign in 1 | Sign in 2 |
|---|---|---|
| User | demo.user@example.com | demo.user@example.com |
| Time (UTC) | 13:02 | 13:41 |
| IP | 203.0.113.10 | 198.51.100.77 |
| Location | Bridgetown, Barbados | Amsterdam, Netherlands |
| App | Microsoft Outlook | Microsoft 365 portal |
| Device | Managed company laptop | Unknown browser, unmanaged |
| Result | Success, MFA completed | Success, MFA satisfied by claim in token |
| IP reputation | Known company ISP | Tagged PROXY |

**What stands out:** 39 minutes between two countries thousands of kilometers apart. Sign in 2 used an unmanaged device, a proxy IP, and didn't prompt for new MFA. That last detail can mean a **stolen session token** was reused.

## What to check

Use the 5 W's from [Start Here](../00-start-here/).

| Question | What to find |
|---|---|
| **Who?** | Which user? Are they a frequent traveler, admin, or high value account? |
| **What?** | Which apps were accessed in each sign in? Did either sign in succeed? |
| **Where?** | Locations, IPs, and IP reputation for **both** sign ins |
| **When?** | Time between sign ins. Is it physically possible? |
| **Why / How?** | VPN, travel, mobile, or something else? What authentication method was used? |

## Triage and Response

1. **Collect details for both sign ins.** User, times, IPs, locations, apps, devices, authentication details, and results.
2. **Check if both sign ins succeeded.** A failed attempt from a distant location is less urgent than a successful one.
3. **Check IP reputation for both IPs.** Use IPinfo, AbuseIPDB, or GreyNoise. Is one a VPN, proxy, hosting provider, or flagged IP? In true positive cases, one IP often looks normal and the other looks suspicious.
4. **Compare devices.** Is one device managed and the other unknown?
5. **Review authentication details.** Did the second sign in complete MFA, or was MFA satisfied by an existing token?
6. **Review the user's sign in history.** Has the user signed in from either location or IP before?
7. **Check for follow up activity.** After the suspicious sign in, look for new inbox rules, password or MFA changes, file downloads, or app consents.
8. **Contact the user.** Ask if they're traveling, using a VPN, or recognize the activity. Use the [User Confirmation Template](../00-start-here/user-confirmation-template.md).

## Decision Logic

| What you found | Action |
|---|---|
| User confirms travel or VPN use, and both sign ins match their devices | Close as true positive: benign. Document who confirmed it |
| One IP is your company's VPN or a known cloud service | Close as false positive. Suggest the team add the IP range as a trusted location |
| User can't be reached, and one sign in used an unknown device or proxy IP | Escalate to Tier 2 |
| User says they didn't make one of the sign ins | **Escalate immediately as possible account compromise** |
| Suspicious sign in followed by inbox rule, MFA, or password changes | **Escalate immediately as a potential incident** |
| MFA satisfied by token on an unknown device from a flagged IP | **Escalate immediately. Possible session token theft** |

## Escalation

When escalating, include:

- User, both sign in times, IPs, locations, apps, and devices
- IP reputation results for both IPs
- Authentication details for both sign ins
- Any follow up activity after the suspicious sign in
- Whether the user confirmed or denied the activity
- Your recommendation (for example: revoke sessions, reset password, review MFA methods)

Use the [Escalation Message Template](../00-start-here/escalation-message-template.md) to keep it clear.

## References

- [Microsoft Entra ID Protection risk detections](https://learn.microsoft.com/en-us/entra/id-protection/concept-identity-protection-risks)
- [How to investigate Defender for Cloud Apps anomaly alerts](https://learn.microsoft.com/defender-cloud-apps/investigate-anomaly-alerts)
