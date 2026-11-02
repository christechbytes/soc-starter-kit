# Runbook: Discovery:S3/AnomalousBehavior

**Severity:** Low

**Tier:** 1

**Data source:** CloudTrail data events for S3

## Description

A user or role in your AWS account looked through the contents of an S3 bucket in a way that is unusual for them. Think of someone opening filing cabinets they don't normally open.

## Background

> **What is S3?** Amazon S3 (Simple Storage Service) is AWS's file storage service. Files are stored as "objects" inside containers called "buckets."

> **What is an IAM entity?** IAM (Identity and Access Management) controls who can do what in AWS. An IAM entity is a user or role, basically an identity that can take actions.

GuardDuty learns what normal S3 activity looks like for each user or role. This finding fires when one of them calls an API used to list or discover S3 contents, such as `ListObjects`, in a way that doesn't match their usual pattern. For example:

- A user with no previous history of using S3 suddenly lists bucket contents
- A user calls the API from an unusual location

Attackers often do this right after stealing credentials. Before taking data, they look around to see what's there. That's why this is a **Discovery** finding: it can be an early warning before something worse.

It's also commonly triggered by legitimate changes, like a new script, a new team member, or a developer working from a new location.

**Related finding to watch for:**

| Finding | Meaning | Severity |
|---|---|---|
| `Exfiltration:S3/AnomalousBehavior` | The same kind of unusual behavior, but involving APIs used to collect data | High |

## Example finding (fictional)

All values below are fictional. `111122223333` is the example account ID AWS uses in its documentation, and `198.51.100.23` is from an IP range reserved for documentation.

| Field | Value |
|---|---|
| Account | 111122223333 |
| Region | us-east-1 |
| IAM user | demo-reporting-user |
| Bucket | demo-customer-exports |
| API called | ListObjects |
| Remote IP | 198.51.100.23 |
| First seen / Last seen | 2026-02-03 22:41 UTC / 2026-02-03 22:58 UTC |

## What you'd see in your SIEM

GuardDuty findings arrive in most SIEMs as JSON (structured text). Field names can vary depending on how your SIEM ingests GuardDuty, but the key fields usually look like this:

```json
{
  "detail": {
    "type": "Discovery:S3/AnomalousBehavior",
    "severity": 2,
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
          "api": "ListObjects",
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
| `s3BucketDetails.name` | Which bucket was looked at |
| `api` | What action was taken |
| `ipAddressV4` | Where the request came from |

### Example search (Datadog)

Queries depend on how your company sends GuardDuty into Datadog, so check with your team before relying on these. Two starting points:

**Search every field for the user:**
```
*:demo-reporting-user
```

**Narrow to GuardDuty findings (if your setup tags them this way):**
```
source:guardduty *:demo-reporting-user
```

**Tip:** Find one known GuardDuty finding in your Log Explorer and look at its tags and fields. That shows you how your environment labels them.

> **Note:** S3 object level activity comes from CloudTrail data events, which not every company sends to its SIEM. If you can't find the underlying activity in your SIEM, check the GuardDuty console or ask your cloud team.

> **Note:** This example uses Datadog, the SIEM I've worked with. I haven't used Splunk or other SIEMs yet, so I've left those out rather than guess. Contributions with examples for other SIEMs are welcome.

## Triage and Response

1. **Confirm the finding details.** Note the account, region, user or role, bucket, API, IP, and first and last seen times.
2. **Identify the user or role.** Is it a person or an automated service? Check your directory or ask the account owner who it belongs to.
3. **Ask: is this normal for them?** Does this user's job involve this bucket? Did they recently start a new project, script, or role?
4. **Check the source IP and location.** Is it a known company IP, VPN, or cloud service? Or an unfamiliar location? Look it up in AbuseIPDB or VirusTotal.
5. **Search for related findings.** Look for other GuardDuty findings involving the same user or IP in the last 7 days, especially `Exfiltration:S3/AnomalousBehavior`.
6. **Check the bucket's sensitivity.** Does it hold customer data, backups, or anything confidential?

## Decision Logic

| What you found | Action |
|---|---|
| User confirmed the activity, and it fits their work | Close as expected behavior. Document who confirmed it |
| Automated script or service, recently deployed or changed | Confirm with the owner, then close and document |
| User can't be reached, and the bucket is sensitive | Escalate to Tier 2 |
| User says it wasn't them | **Escalate immediately as possible compromised credentials** |
| Unfamiliar or malicious IP | Escalate to Tier 2 |
| An `Exfiltration:S3/AnomalousBehavior` finding for the same user | **Escalate immediately as a potential incident** |

## Escalation

When escalating, include:

- Finding ID, account, region, user or role, and bucket
- API called, source IP, and reputation results
- Whether the user or owner confirmed the activity
- Any related findings
- How sensitive the bucket is
- Your recommendation (for example: disable the access key, review the user's recent activity)

## References

- [AWS GuardDuty S3 Protection finding types](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types-s3.html)
- [AWS GuardDuty finding types](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types.html)
