# Runbooks

## What is a runbook?

A runbook is a step by step guide for handling a specific security alert. It tells you what the alert means, what to check, and when to escalate. Runbooks help analysts respond the same way every time, even on a busy shift.

## How each runbook is organized

| Section | What it tells you |
|---|---|
| Description | What the alert means in plain language |
| Background | Why it matters and what usually causes it |
| Example finding | A fictional example of what the alert looks like |
| What you'd see in your SIEM | Key fields and example searches |
| Triage and Response | The steps to investigate |
| Decision Logic | What to do based on what you found |
| Escalation | What to include when passing it to Tier 2 |

## Runbooks in this folder

| Runbook | Service | Severity |
|---|---|---|
| [Discovery:RDS/MaliciousIPCaller](discovery-rds-maliciousipcaller.md) | Amazon RDS | Medium |
| [Discovery:S3/AnomalousBehavior](discovery-s3-anomalousbehavior.md) | Amazon S3 | Low |

More runbooks are added as they're finished.

## Before you use these

- All examples use fictional data
- Every company's environment is set up differently, so adjust searches and escalation paths to match yours
- When in doubt, escalate. Nobody gets in trouble for asking
