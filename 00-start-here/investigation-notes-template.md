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
