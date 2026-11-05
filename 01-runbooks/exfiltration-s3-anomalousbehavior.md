# Runbook: Exfiltration:S3/AnomalousBehavior

**Severity:** High

**Data source:** CloudTrail data events for S3

## Description

A user or role in your AWS account accessed S3 data in a way that is unusual for them, using actions associated with taking data. Think of someone not just opening filing cabinets, but carrying files out the door.

## Background

> **What is exfiltration?** Exfiltration means data being moved out of an environment without authorization. It's often the attacker's end goal.

GuardDuty learns what normal S3 activity looks like for each user or role. This finding fires when one of them makes S3 API calls that don't match their usual pattern, and the API is one associated with collecting data. For example:

- A user with no previous history of using S3 suddenly accesses bucket data
- A user accesses data from an unusual location

**Why this is High severity:** Discovery findings mean someone may be looking around. Exfiltration findings mean data may already be leaving. Treat this one with urgency.

**Related finding to watch for:**

| Finding | Meaning | Severity |
|---|---|---|
| `Discovery:S3/AnomalousBehavior` | The same user may have been looking around first. See the [Discovery:S3 runbook](discovery-s3-anomalousbehavior.md) | Low |

If both appear for the same user, close together in time, that's a strong pattern: **look, then take.**

## Example finding (fictional)

This continues the scenario from the Discovery:S3 runbook. All values are fictional. `111122223333` is the example account ID AWS uses in its documentation, and `198.51.100.23` is from an IP range reserved for documentation.

| Field | Value |
|---|---|
| Account | 111122223333 |
| Region | us-east-1 |
| IAM user | demo-reporting-user |
| Bucket | demo-customer-exports |
| API called | GetObject (downloading files) |
| Remote IP | 198.51.100.23 |
| First seen / Last seen | 2026-02-03 23:05 UTC / 2026-02-03 23:40 UTC |

## What you'd see in your SIEM

GuardDuty findings arrive in most SIEMs as JSON (structured text). Field names can vary depending on how your SIEM ingests GuardDuty, but the key fields usually look like this:

```json
{
  "detail": {
    "type": "Exfiltration:S3/AnomalousBehavior",
    "severity": 8,
    "accountId": "111122223333",
    "region": "us-east-1",
    "resource": {
      "accessKeyDetails": {
        "userName": "demo-reporting-user"
      },
      "s3BucketDetails": [
        { "name": "demo-customer-exports" }
      ]
    },
    "service": {
      "action": {
        "actionType": "AWS_API_CALL",
        "awsApiCallAction": {
          "api": "GetObject",
          "remoteIpDetails": {
            "ipAddressV4": "198.51.100.23"
          }
        }
      }
    }
  }
}
```

### Fields to check first

| Field | Why it matters |
|---|---|
| `type` | Confirms which finding this is |
| `userName` | Who did it. This is the most important field |
| `s3BucketDetails.name` | Which bucket the data came from |
| `api` | What action was taken |
| `ipAddressV4` | Where the request came from |

### Example search (Datadog)

Queries depend on how your company sends GuardDuty into Datadog, so check with your team before relying on these.

**Search every field for the user:**
```
*:demo-reporting-user
```

**Narrow to GuardDuty findings (if your setup tags them this way):**
```
source:guardduty *:demo-reporting-user
```

> **Note:** S3 object level activity comes from CloudTrail data events, which not every company sends to its SIEM. If you can't find the underlying activity in your SIEM, check the GuardDuty console or ask your cloud team.

> **Note:** This example uses Datadog, the SIEM I've worked with. I haven't used Splunk or other SIEMs yet, so I've left those out rather than guess. Contributions with examples for other SIEMs are welcome.

## Triage and Response

Because this is High severity, work quickly and escalate early if anything is unclear.

1. **Confirm the finding details.** Note the account, region, user or role, bucket, API, IP, and first and last seen times.
2. **Check for a matching Discovery finding.** Search for `Discovery:S3/AnomalousBehavior` for the same user in the last 7 days. Looking then taking is a strong warning sign.
3. **Identify the user or role.** Is it a person or an automated service? Who owns it?
4. **Check the bucket's sensitivity.** Does it hold customer data, backups, credentials, or anything confidential?
5. **Check the source IP and location.** Known company IP or VPN? Or unfamiliar? Look it up in AbuseIPDB, IPinfo, or VirusTotal.
6. **Contact the user or owner.** Ask directly whether they did this and why.

## Decision Logic

| What you found | Action |
|---|---|
| User confirmed the activity, it fits their work, and the IP is known | Close as true positive: benign. Document who confirmed it |
| Approved automated job, recently deployed or changed | Confirm with the owner and check the change log, then close and document |
| User can't be reached quickly | **Escalate immediately.** Don't wait on High severity |
| User says it wasn't them | **Escalate immediately as possible compromised credentials** |
| A matching Discovery finding for the same user | **Escalate immediately as a potential incident** |
| Sensitive bucket and unfamiliar IP | **Escalate immediately as a potential incident** |

## Escalation

When escalating, include:

- Finding ID, account, region, user or role, and bucket
- API called, source IP, and reputation results
- Any related findings, especially a matching Discovery finding
- How sensitive the bucket is
- Whether the user or owner confirmed the activity
- Your recommendation (for example: disable the access key, review what was accessed)

Use the [Escalation Message Template](../00-start-here/escalation-message-template.md) to keep it clear.

## References

- [AWS GuardDuty S3 Protection finding types](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types-s3.html)
- [AWS GuardDuty finding types](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types.html)
