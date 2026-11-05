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
