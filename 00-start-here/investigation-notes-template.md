# Investigation Notes Template

Copy this for every alert you investigate. Fill it in as you go, not at the end.

## Alert details

| Field | Entry |
|---|---|
| Alert name | |
| Alert ID | |
| Severity | |
| Source tool | |
| Time received | |
| Analyst | |

## The 5 W's

| Question | Answer |
|---|---|
| **Who?** (user, role, service, or IP) | |
| **What?** (action taken) | |
| **Where?** (system affected, source location) | |
| **When?** (first seen, last seen, how often) | |
| **Why / How?** (legitimate reason? how it happened?) | |

## Timeline

| Time (UTC) | What happened | Source |
|---|---|---|
| | | |
| | | |

## Evidence checked

- [ ] IP reputation lookup
- [ ] Related alerts for the same user, IP, or system (last 7 days)
- [ ] Change log for approved changes
- [ ] Asset owner or user contacted
- [ ] Other:

## Findings

Write what you found in two or three sentences.

## Classification

- [ ] True positive: malicious
- [ ] True positive: benign
- [ ] False positive

## Actions taken

- 

## Next steps

- [ ] Close and document
- [ ] Escalate to Tier 2 (use the [Escalation Message Template](escalation-message-template.md))
- [ ] Suggest rule tuning








---

# Example (fictional)

## Alert details

| Field | Entry |
|---|---|
| Alert name | Discovery:S3/AnomalousBehavior |
| Alert ID | EXAMPLE-0001 |
| Severity | Low |
| Source tool | AWS GuardDuty (via SIEM) |
| Time received | Feb 3, 23:02 UTC |
| Analyst | Demo Analyst |

## The 5 W's

| Question | Answer |
|---|---|
| **Who?** (user, role, service, or IP) | IAM user `demo-reporting-user`, from IP `198.51.100.23` |
| **What?** (action taken) | Called `ListObjects` on an S3 bucket |
| **Where?** (system affected, source location) | Bucket `demo-customer-exports` (customer data), us-east-1 |
| **When?** (first seen, last seen, how often) | 22:41 to 22:58 UTC. First time this user has accessed S3 |
| **Why / How?** (legitimate reason? how it happened?) | Unknown. Manager couldn't confirm, user not reachable |

## Timeline

| Time (UTC) | What happened | Source |
|---|---|---|
| 22:41 | First `ListObjects` call on the bucket | GuardDuty finding |
| 22:58 | Last `ListObjects` call | GuardDuty finding |
| 23:02 | Alert received in SIEM | SIEM |
| 23:10 | Contacted user's manager. Couldn't confirm the activity | Phone call |
| 23:15 | Messaged the user. No reply | Chat |

## Evidence checked

- [x] IP reputation lookup: no reports, not a known company IP
- [x] Related alerts for the same user, IP, or system (last 7 days): none found
- [x] Change log for approved changes: no matching record
- [x] Asset owner or user contacted: manager couldn't confirm, user not reachable
- [ ] Other:

## Findings

A user with no history of S3 access listed the contents of a bucket holding customer data, from an IP not linked to the company. The activity can't be confirmed as legitimate, and the bucket is sensitive.

## Classification

- [ ] True positive: malicious
- [ ] True positive: benign
- [ ] False positive

Not yet classified. Unconfirmed, possible compromised credentials.

## Actions taken

- Contacted the user's manager and the user
- Checked IP reputation, related alerts, and the change log

## Next steps

- [ ] Close and document
- [x] Escalate to Tier 2 (see the example in the [Escalation Message Template](escalation-message-template.md))
- [ ] Suggest rule tuning
