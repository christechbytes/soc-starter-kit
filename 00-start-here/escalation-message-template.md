# Escalation Message Template

A good escalation lets the next person act without asking you questions. Keep it short and factual.

## Template

**Subject:** [Severity] [Alert name] on [system or user]

**Summary:** One sentence on what happened.

**Who:**
**What:**
**Where:**
**When:**
**Why / How:** What's known and what's still unknown.

**Evidence checked:**
-
-

**Classification so far:**

**Recommended action:**

**Alert ID / ticket link:**

## Example (fictional)

**Subject:** [Low] Discovery:S3/AnomalousBehavior on demo-reporting-user

**Summary:** An IAM user listed the contents of a sensitive S3 bucket for the first time, and the user's manager can't confirm the activity.

**Who:** IAM user `demo-reporting-user`, from IP `198.51.100.23`
**What:** Called `ListObjects` on the bucket
**Where:** Bucket `demo-customer-exports` (holds customer data), us-east-1
**When:** Feb 3, 22:41 to 22:58 UTC. No previous history of this user accessing S3
**Why / How:** Unknown. Manager contacted at 23:10 UTC, couldn't confirm. User not reachable

**Evidence checked:**
- IP reputation: no reports, not a known company IP
- No related GuardDuty findings in the last 7 days
- No matching change record

**Classification so far:** Unconfirmed. Possible compromised credentials

**Recommended action:** Review the user's recent activity and consider disabling the access key until the user confirms

**Alert ID / ticket link:** EXAMPLE-0001

## Tips

- **Lead with the summary.** The reader should know what's going on in one sentence
- **Separate known from unknown.** Don't guess. Say "unknown" and what you tried
- **Include what you already checked** so nobody repeats your work
- **Recommend, don't decide.** Suggest an action, and let Tier 2 make the call
