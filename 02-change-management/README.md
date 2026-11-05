# Change Management for SOC Analysts

## Why a SOC analyst should care

Every change to a system, like a new firewall rule, a new user, or a config update, leaves a trail in the logs. Attackers make changes too. When a signal fires about a change, your first question is:

**"Was this change approved?"**

The change log answers that. Without one, you can't tell an approved update from an attacker covering their tracks.

## How it connects to investigations

| What you found | What the change log tells you | Classification |
|---|---|---|
| A firewall rule was modified | Change was approved and scheduled for that time | True positive: benign |
| A new admin account was created | No matching change record exists | Escalate. Possible true positive: malicious |
| A config changed outside the approved window | A record exists, but timing doesn't match | Ask the change owner, then escalate if unexplained |

## What makes a good change record

A useful change record answers the same 5 W's you use in investigations:

| Question | What the record should include |
|---|---|
| **Who?** | Who requested it, who approved it, who made it |
| **What?** | Exactly what was changed |
| **Where?** | Which system or device |
| **When?** | Planned date and time, and when it was actually done |
| **Why / How?** | The reason for the change and how it was tested or can be reversed |

## Why auditors care

Many companies are audited against **SOC 2**, a security standard. Its change management criterion (CC8.1) expects changes to be authorized, tested, approved, and documented. Undocumented changes are one of the most common audit findings.

> **Note:** In October 2026 the AICPA released a draft of proposed 2027 SOC 2 criteria that would reorganize change management under different criteria numbers. It's a draft, so check the current standard if you're working on an audit.

## Files in this folder

| File | What it is |
|---|---|
| [Templates](templates.md) | A blank change request form and change log you can copy |

## References

- [SOC 2 change management controls (CC8.1)](https://soc2auditors.org/insights/soc-2-change-management-controls/)
- [Proposed 2027 SOC 2 Trust Services Criteria](https://soc2auditors.org/insights/soc-2-trust-services-criteria-2027/)
