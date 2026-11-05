# Start Here

New to security monitoring? Read this before opening any runbook.

## What is a signal?

A **signal** (also called an alert or finding) is a notification that something happened that might be worth a look. Security tools watch huge amounts of activity, like logins, file access, and network traffic, and raise a signal when something matches what they're looking for.

**A signal is not proof of an attack.** It's a question: "Is this okay?" Your job is to answer it.

## Why do signals trigger?

Most signals fire for one of four reasons:

| Trigger type | What it means | Example |
|---|---|---|
| **Rule match** | Activity matched a pattern someone wrote | A user logged in from two countries within an hour |
| **Threshold** | Something happened more times than expected | 50 failed logins in 5 minutes |
| **Threat intelligence** | Activity involved a known bad IP, domain, or file | A database was probed by an IP on a threat list |
| **Anomaly** | Activity is unusual compared to normal behavior | A user accessed a bucket they've never touched before |

Knowing the trigger type helps you investigate. A threat intel match asks "how bad is this source?" An anomaly asks "is there a good reason for this change?"

## What happens after you investigate?

Every signal ends with one of these classifications:

| Classification | Meaning | Example | Action |
|---|---|---|---|
| **True positive: malicious** | The activity really happened and it's harmful | Stolen credentials used to list a sensitive bucket | Escalate |
| **True positive: benign** | The activity really happened, but it's expected or approved | A new reporting script listing a bucket for the first time | Close and document who confirmed it |
| **False positive** | The tool got it wrong. The activity isn't what it looked like | A detection rule misread a routine backup job as suspicious | Close, document, and suggest tuning the rule |

**Why the difference matters:** a true positive benign means the tool worked correctly, the activity just turned out to be fine. A false positive means the tool itself needs fixing. Mixing them up hides problems with your detections.

## How to investigate: the 5 W's

Every investigation starts by answering five questions. If you can't answer one, that's what you dig into next.

| Question | What to find | Where to look |
|---|---|---|
| **Who?** | The user, role, service, or IP involved | User name, account, source IP |
| **What?** | The action that was taken | API call, login, file access, command run |
| **Where?** | The system affected and where the activity came from | Server, database, bucket, region, location |
| **When?** | When it started, when it stopped, how often | First seen, last seen, count |
| **Why / How?** | Is there a legitimate reason? How did it happen? | Ask the owner, check recent changes, related signals |

### Example (fictional)

A `Discovery:S3/AnomalousBehavior` finding arrives:

| Question | Answer |
|---|---|
| **Who?** | IAM user `demo-reporting-user` from IP `198.51.100.23` |
| **What?** | Listed the contents of an S3 bucket |
| **Where?** | Bucket `demo-customer-exports` in us-east-1 |
| **When?** | Feb 3, 22:41 to 22:58 UTC. First time this user has done this |
| **Why / How?** | Unknown yet. Next step: contact the user's manager to confirm |

Writing the 5 W's down like this makes it easy to see what you know, what's missing, and what to include if you escalate.

## Tips for beginners

- **Start with Who.** Knowing who did something answers half the question
- **Context beats severity.** A Low severity signal on a sensitive system can matter more than a Medium on a test system
- **Write as you go.** Notes made during an investigation are easier than reconstructing later
- **Escalating isn't failing.** If something doesn't add up, pass it on with your notes

## Don't recognize something? Look it up

You'll constantly see signal names, API calls, and terms you don't recognize. That's normal, even for experienced analysts. Nobody memorizes them all.

When something is unfamiliar:

1. **Search the exact name.** For example, search `Discovery:S3/AnomalousBehavior` or `ListObjects AWS`
2. **Prefer official sources first.** Vendor documentation (AWS, Microsoft, your SIEM vendor) explains what the signal or API actually does
3. **Get the gist, then apply it.** You don't need to become an expert. Just understand enough to answer: what does this action do, and why might an attacker use it?
4. **Use what you learned to fill in your 5 W's.** Knowing what an API does often answers the **What** and helps you judge the **Why / How**

Looking things up isn't a weakness. It's a core part of the job.

## Helpful tools

| Tool | What it's for |
|---|---|
| [IPinfo](https://ipinfo.io/) | Where an IP is located and who owns it (company, ISP, hosting provider) |
| [AbuseIPDB](https://www.abuseipdb.com/) | Whether an IP has been reported for malicious activity, like scanning or brute force |
| [VirusTotal](https://www.virustotal.com/) | Reputation checks for IPs, domains, URLs, and file hashes across many security vendors |
| [GreyNoise](https://viz.greynoise.io/) | Whether an IP is just scanning the whole internet (background noise) or may be targeting you |
| [MITRE ATT&CK](https://attack.mitre.org/) | A library of attacker techniques. Helps explain why an attacker might take an action |
| [AWS GuardDuty finding types](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types.html) | Official explanations of every GuardDuty finding |

> **Careful what you paste.** These are public websites. Looking up a public IP is fine, but never paste internal IPs, usernames, company names, or upload files from work. Anything uploaded to VirusTotal can be visible to other users. Search by file hash instead, and follow your company's rules.

## Templates

| Template | Use it for |
|---|---|
| [Investigation Notes](investigation-notes-template.md) | Recording your work on any alert using the 5 W's |
| [Escalation Message](escalation-message-template.md) | Handing an alert to Tier 2 clearly |
| [User Confirmation](user-confirmation-template.md) | Asking a user to confirm whether an action was theirs |

## Next steps

Once you're comfortable with the 5 W's, open the [Runbooks](../01-runbooks/) folder. Each runbook applies this approach to a specific signal.
