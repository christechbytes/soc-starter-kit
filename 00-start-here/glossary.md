# Glossary

Plain language definitions of terms used in this repo, in alphabetical order.

| Term | Meaning |
|---|---|
| **Access key** | A credential that lets a user or program connect to AWS without a password. If stolen, an attacker can act as that user. The first four letters show what type it is. See [AWS key and ID prefixes](#aws-key-and-id-prefixes) below |
| **Alert / Signal / Finding** | A notification that something happened that may need investigating. Different tools use different names for the same idea |
| **Anomaly** | Activity that's unusual compared to normal behavior. Not automatically bad, but worth checking |
| **API / API call** | A request one program sends to another to do something, like "list these files." In AWS, almost every action is an API call |
| **Asset** | Any device, system, or account a company owns or manages |
| **Baseline** | What normal activity looks like for a user, system, or network |
| **Bucket** | A container for storing files in Amazon S3 |
| **C2 (Command and Control)** | Servers attackers use to send instructions to infected devices |
| **Control Tower** | An AWS service that sets up and governs multi account environments using security rules called controls |
| **Change log** | A record of approved changes to systems. Used to check if a change was expected |
| **CloudTrail** | An AWS service that records actions taken in an AWS account |
| **Detection rule** | Logic that tells a security tool what to alert on |
| **Escalation** | Passing an alert to a more senior analyst or team for further action |
| **EventBridge** | An AWS service that routes events from one service to another |
| **Exfiltration** | Data being moved out of an environment without authorization |
| **False positive** | An alert where the tool got it wrong. The activity isn't what it looked like |
| **Firewall** | A device or service that allows or blocks network traffic based on rules |
| **GuardDuty** | An AWS service that detects suspicious activity and creates findings |
| **IAM (Identity and Access Management)** | Controls who can do what in AWS |
| **IAM entity** | A user or role in AWS. An identity that can take actions |
| **Incident** | A confirmed security event that needs a coordinated response |
| **IOC (Indicator of Compromise)** | Evidence that something malicious happened, like a known bad IP or file hash |
| **IPS (Intrusion Prevention System)** | A firewall feature that detects and blocks known attack patterns |
| **JSON** | A structured text format many tools use to store and send data |
| **Lambda** | An AWS service that runs small pieces of code without needing a server |
| **Log** | A record of an event, like a login or file access |
| **MITRE ATT&CK** | A public library of attacker techniques, used to understand and describe attacks |
| **OU (Organizational Unit)** | A group of AWS accounts inside an organization, used to apply the same rules to all of them |
| **Pipeline** | The chain of services that moves logs or alerts from one place to another |
| **Policy (firewall)** | A rule that decides whether network traffic is allowed or blocked |
| **RDS (Relational Database Service)** | AWS's managed database service |
| **Runbook** | A step by step guide for handling a specific alert |
| **S3 (Simple Storage Service)** | AWS's file storage service |
| **SCP (Service Control Policy)** | A policy that sets the maximum permissions for accounts in an AWS organization. It limits access but never grants it |
| **Severity** | How serious a tool rates an alert, often Low, Medium, High, or Critical |
| **SIEM** | A tool that collects logs and alerts from many sources in one place so analysts can search and investigate. Examples include Datadog and Splunk |
| **SOC (Security Operations Center)** | The team that monitors for and responds to security threats |
| **SOC 2** | An audit standard that checks how a company protects customer data. Not the same as a SOC team |
| **Threat intelligence** | Information about known threats, like bad IPs, domains, or attacker techniques |
| **Tier 1 / Tier 2** | SOC levels. Tier 1 does first triage. Tier 2 handles deeper investigation and escalations |
| **Tor** | A network that hides a user's location. Legitimate uses exist, but attackers often use it too |
| **Triage** | Quickly assessing an alert to decide how serious it is and what to do next |
| **True positive: benign** | The activity really happened, but it's expected or approved |
| **True positive: malicious** | The activity really happened and it's harmful |
| **VPN (Virtual Private Network)** | An encrypted connection, often used to reach a company network remotely |




## AWS key and ID prefixes

In AWS, the first four characters of a key or ID tell you what type it is. This helps during triage, especially when a finding shows an access key.

### Access keys

| Prefix | What it is | Lasts | Why it matters in triage |
|---|---|---|---|
| **AKIA** | Long term access key, tied to an IAM user | Until someone deletes or rotates it | If stolen, it keeps working until it's disabled. Treat suspicious AKIA activity urgently |
| **ASIA** | Temporary access key, created by AWS STS (Security Token Service), often when someone assumes a role or signs in through single sign on | A limited time, then it expires | Look at **who or what created the session**. The source identity matters more than the key itself |

### Other IDs you may see

| Prefix | What it identifies |
|---|---|
| **AIDA** | An IAM user |
| **AROA** | An IAM role |
| **AGPA** | An IAM user group |
| **AIPA** | An EC2 instance profile (lets a server use a role) |

### Quick triage tip

- **AKIA in a suspicious finding:** ask whether that user should even have a long term key. Many companies try to avoid them
- **ASIA in a suspicious finding:** find the role or user behind the session, then investigate that identity
- **Expiry isn't safety.** A temporary key can still do damage before it expires

Full list: [AWS IAM identifiers](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html)
