# Runbook: Discovery:RDS/MaliciousIPCaller

**Severity:** Medium
**Tier:** 1
**Data source:** GuardDuty RDS Protection (RDS login activity monitoring)

## Description

A known malicious IP address probed an RDS database in your account. **No login attempt was made.** The attacker knocked on the door but didn't try the handle.

## Background

> **What is RDS?** Amazon RDS (Relational Database Service) is AWS's managed database service. It runs common databases like MySQL and PostgreSQL for you, so you don't have to build and maintain the servers yourself. Amazon Aurora is AWS's own database engine that also runs under RDS.

GuardDuty RDS Protection watches login activity on supported Amazon RDS and Aurora databases. It compares the source IP against threat intelligence lists of known bad addresses.

This finding is usually a sign of **internet wide scanning**: automated tools probing many databases looking for weak targets. On its own it is rarely a breach, but it tells you something important: **your database may be reachable from the internet.**

Related findings to watch for from the same IP:

| Finding | Meaning | Severity |
|---|---|---|
| `CredentialAccess:RDS/MaliciousIPCaller.FailedLogin` | They tried to log in and failed | Medium |
| `CredentialAccess:RDS/MaliciousIPCaller.SuccessfulLogin` | They logged in | High |

## Example finding (fictional)

All values below are fictional. `111122223333` is the example account ID AWS uses in its documentation, and `203.0.113.45` is from an IP range reserved for documentation.

| Field | Value |
|---|---|
| Account | 111122223333 |
| Region | us-east-1 |
| Resource | demo-orders-db |
| Remote IP | 203.0.113.45 |
| Action type | RDS_LOGIN_ATTEMPT |
| First seen / Last seen | 2026-01-15 02:14 UTC / 2026-01-15 02:16 UTC |

## What you'd see in your SIEM

GuardDuty findings arrive in most SIEMs as JSON (structured text). Field names can vary depending on how your SIEM ingests GuardDuty, but the key fields usually look like this:

```json
{
  "detail": {
    "type": "Discovery:RDS/MaliciousIPCaller",
    "severity": 5,
    "accountId": "111122223333",
    "region": "us-east-1",
    "resource": {
      "rdsDbInstanceDetails": {
        "dbInstanceIdentifier": "demo-orders-db"
      }
    },
    "service": {
      "action": {
        "actionType": "RDS_LOGIN_ATTEMPT",
        "rdsLoginAttemptAction": {
          "remoteIpDetails": {
            "ipAddressV4": "203.0.113.45"
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
| `severity` | 5 means Medium |
| `dbInstanceIdentifier` | Which database was probed |
| `ipAddressV4` | Who probed it. Use this for reputation lookups |

### Example search (Datadog)

Use this to find related findings from the same IP:

```
source:guardduty "203.0.113.45"
```

**Tip:** Searching for the IP as plain text is the easiest starting point. It catches the IP no matter which field it sits in.

> **Note:** This example uses Datadog, the SIEM I've worked with. I haven't used Splunk or other SIEMs yet, so I've left those out rather than guess. If you use a different SIEM, the same approach applies: search for the IP and look for related findings. Contributions with examples for other SIEMs are welcome.

## Triage and Response

1. **Confirm the finding details.** Note the account, region, database name, remote IP, and first and last seen times.
2. **Check the IP's reputation.** Look it up in a threat intel source such as AbuseIPDB or VirusTotal. Note the country and any reports of scanning.
3. **Search for related findings.** In your SIEM, search for other GuardDuty findings involving the same IP or the same database in the last 7 days.
4. **Check whether the database should be public.** Ask the database owner or check the asset inventory. Is this database meant to be reachable from the internet?
5. **Check the time window.** A short burst (a few minutes) suggests automated scanning. Repeated probes over days suggest targeting.

## Decision Logic

| What you found | Action |
|---|---|
| Single probe, no related findings, database is meant to be public | Close as benign scanning. Document the IP and reasoning |
| Database is **not** meant to be public | Escalate to Tier 2. This is a configuration issue |
| A `FailedLogin` finding from the same IP | Escalate to Tier 2. The attacker moved from looking to trying |
| A `SuccessfulLogin` finding from the same IP | **Escalate immediately as a potential incident** |
| Repeated probes over multiple days | Escalate to Tier 2 for possible IP blocking |

## Escalation

When escalating, include:

- Finding ID, account, region, and database name
- Remote IP and reputation results
- Any related findings
- Whether the database is meant to be public
- Your recommendation (for example: restrict database access, block the IP)

## References

- [AWS GuardDuty finding types](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types.html)
- [AWS Security Blog: Detect threats to data in RDS using GuardDuty](https://aws.amazon.com/blogs/security/detect-threats-to-your-data-stored-in-rds-databases-by-using-guardduty/)
