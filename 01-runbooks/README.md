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
| [Exfiltration:S3/AnomalousBehavior](exfiltration-s3-anomalousbehavior.md) | Amazon S3 | High |
| [Impossible Travel / Atypical Travel](impossible-travel.md) | Microsoft Entra ID | Varies |

More runbooks are added as they're finished.

## Searching your SIEM

Each runbook shows example Datadog searches. Before relying on them:

- **Queries depend on your setup.** GuardDuty can reach Datadog through different routes (for example the Datadog Forwarder or Amazon Kinesis Firehose), and each can label logs differently. Your team may also use custom pipelines that rename fields or tags
- **Start from a known finding.** Open one GuardDuty finding in your Log Explorer and look at its tags and fields. Build your searches from there
- **Some activity may be missing.** S3 object level activity comes from CloudTrail data events, which not every company sends to its SIEM. Runtime findings may not be forwarded either. If you can't find something, check the GuardDuty console or ask your cloud team
- **Other SIEMs:** I've only worked with Datadog, so I've left other SIEMs out rather than guess. The approach is the same: search for the IP or user and look for related findings. Contributions are welcome

## Before you use these

- All examples use fictional data
- Every company's environment is set up differently, so adjust searches and escalation paths to match yours
- When in doubt, escalate. Nobody gets in trouble for asking
