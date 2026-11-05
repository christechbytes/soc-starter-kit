# Firewall Alerts for SOC Analysts

> **My perspective:** During my internship I worked on the firewall side: configuring intrusion prevention (IPS), botnet protection, and helping design which events should trigger alerts. I haven't triaged live firewall alerts yet, so this is a primer on what firewall events mean and why they matter, not a triage runbook.

## What a firewall does

A firewall sits between networks and decides what traffic is allowed through, based on rules called **policies**. Modern firewalls also inspect traffic for attacks and known bad destinations. Every decision can be logged, and those logs often feed into the SIEM.

## Types of firewall events

| Event type | What it means | Why a SOC cares |
|---|---|---|
| **Traffic: allowed** | Traffic matched a policy and was let through | Useful for investigations, but too common to alert on by itself |
| **Traffic: denied** | Traffic was blocked by a policy | Mostly noise from the internet. Interesting when it comes from inside your network |
| **IPS detection** | Traffic matched a known attack pattern | Someone may be trying to exploit a system |
| **Botnet / C2 detection** | A device tried to contact a known malicious server | The device itself may be infected |
| **Admin activity** | Someone logged in to the firewall or changed its settings | Firewall admin access is powerful. Unexpected logins or changes are serious |

> **C2** means command and control: servers attackers use to send instructions to infected devices.

## Key fields to read

Names vary by firewall vendor, but most logs include:

| Field | What it tells you |
|---|---|
| Source IP | Where the traffic came from |
| Destination IP | Where it was going |
| Destination port | Which service it targeted (for example 443 for HTTPS, 3389 for Remote Desktop) |
| Action | Allowed, denied, or blocked |
| Policy ID | Which firewall rule made the decision |
| Attack or signature name | For IPS events, which attack was detected |
| Severity | How serious the vendor rates it |
| User | For admin events, who logged in |

## Signal or noise?

Not every firewall event deserves an alert. Here's how to think about it:

| Event | Usually noise or worth a look? | Why |
|---|---|---|
| Denied inbound traffic from the internet | Usually noise | The internet constantly scans everything. Blocked means the firewall did its job |
| Denied **outbound** traffic from an internal device | Worth a look | Why is something inside trying to reach somewhere it shouldn't? |
| IPS detection against a public facing server | Worth a look | Check if the server is vulnerable to that attack |
| Botnet / C2 detection from an internal device | **High priority** | Even if blocked, the device may be infected |
| Failed admin logins to the firewall | Worth a look | Possible password guessing |
| Admin login from an unfamiliar IP or at an odd time | **High priority** | Confirm it was a real admin |
| Firewall configuration change | Check the change log | Approved change or not? See [Change Management](../02-change-management/) |

## Blocked doesn't mean done

A common beginner mistake is closing an alert because the firewall blocked it. For botnet and outbound alerts, the block only stopped the connection. **It didn't fix the device that tried to make it.** That device still needs to be checked.

## Applying the 5 W's (fictional example)

A botnet detection appears in the SIEM:

| Question | Answer |
|---|---|
| **Who?** | Internal device `10.10.20.15` (a staff laptop, per the asset list) |
| **What?** | Tried to connect to a known botnet server. The firewall blocked it |
| **Where?** | Destination `203.0.113.80` on port 443 |
| **When?** | 14 attempts between 08:02 and 08:30 UTC |
| **Why / How?** | Unknown. Repeated attempts suggest something on the laptop is running automatically |

**Next step:** escalate so the laptop can be checked by the endpoint team, even though every connection was blocked.

All IPs above are fictional. `10.10.20.15` is a private internal address and `203.0.113.80` is from a range reserved for documentation.

## Tips

- **Know your normal.** You can't spot unusual traffic until you know what usual traffic looks like
- **Internal to external is often more interesting than external to internal**
- **Treat firewall admin activity like a high value account.** Whoever controls the firewall controls the network
