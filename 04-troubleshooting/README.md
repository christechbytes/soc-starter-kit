# Troubleshooting: When Security Alerts Stop Arriving

> **Status:** Based on a real investigation I worked on during my internship, rewritten with fictional details. The fix was still in progress when I wrote this. I'll update this page with the confirmed cause and resolution.

## Why silence is dangerous

When alerts stop, it's tempting to think "great, nothing's happening." But silence has two possible meanings:

1. **Nothing suspicious happened** (good)
2. **Your monitoring broke and you've gone blind** (bad)

The second one is worse than a noisy alert, because attacks can happen with nobody seeing them.

## The scenario (fictional)

A SOC uses AWS GuardDuty to detect threats and sends its findings to Datadog, the SIEM. An analyst notices there haven't been any GuardDuty findings in Datadog for weeks. In a busy cloud environment, weeks of total silence is unusual.

## How the pipeline works

Before you can find a break, you need to know the chain. In Datadog's documented setup, GuardDuty findings travel like this:

GuardDuty → EventBridge rule → Datadog Forwarder (Lambda) → Datadog
(detects) (catches findings) (ships them) (shows them)


| Link | What it does |
|---|---|
| **GuardDuty** | Detects suspicious activity and creates findings |
| **EventBridge rule** | Watches for GuardDuty findings and passes them along |
| **Datadog Forwarder** | A small AWS function (Lambda) that sends the findings to Datadog |
| **Datadog** | Receives the findings so analysts can see and investigate them |

If any link breaks, findings stop arriving, even though GuardDuty may still be detecting threats.

> Your company may use a different route (for example Amazon Kinesis Firehose instead of the Forwarder). Map out your own pipeline first.

## How to spot it

1. In your SIEM, search for the most recent GuardDuty finding
2. Note the date and time it arrived
3. Compare it to normal volume. If findings usually arrive daily and the last one was weeks ago, something is wrong

## Troubleshooting step by step

Work through the chain one link at a time, starting from the source.

| Step | What to check | What a break looks like |
|---|---|---|
| 1. GuardDuty | Is GuardDuty still enabled and producing findings? Check the GuardDuty console | Findings exist in the console but not in the SIEM: the break is further down the chain |
| 2. EventBridge rule | Does the rule still exist, and is it **enabled**? | The rule is disabled or was deleted |
| 3. Forwarder | Is the Lambda function still connected to the rule, and is it running without errors? | No trigger attached, or errors in the function's logs |
| 4. Datadog | Are logs arriving but being filtered or labeled differently? | A pipeline or index change is hiding the findings |

**In this investigation,** GuardDuty appeared to be working, which pointed further down the chain. The leading suspect was a **disabled EventBridge rule**, which would stop findings from ever reaching the Forwarder.

## You may not have access to everything

SOC analysts often can't see every part of the pipeline. You might see the SIEM but not the AWS console. That's normal. Your job is to:

- Notice the silence
- Map the pipeline and narrow down where it might be broken
- Bring clear evidence to the team that owns it (for example: "last finding received on this date, GuardDuty console shows findings after that date, suspect the EventBridge rule")

## Lessons learned

- **Monitor the monitoring.** Set up an alert that fires when a log source goes quiet for longer than normal. Silence should be a signal too
- **Know your pipelines.** Draw the chain for every important log source before something breaks
- **Changes can break things quietly.** A disabled rule doesn't throw an error. Check the [change log](../02-change-management/) for changes made around the time the silence started

## References

- [Datadog: Amazon GuardDuty integration](https://docs.datadoghq.com/integrations/amazon_guardduty/)
