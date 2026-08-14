# INC-003 — CEO Phishing Investigation

## Overview

Investigated a credential-phishing incident in **Microsoft Defender XDR** involving an executive user.

The scenario was generated in a controlled Microsoft Defender Attack Simulation Training lab using the **Credential Harvest** technique. I followed a Tier-1 SOC workflow to validate the alert, review user activity, scope the email telemetry with KQL, and determine whether escalation was required.

## Investigation Summary

| | |
|---|---|
| **Platform** | Microsoft Defender XDR |
| **Attack Type** | Credential Phishing |
| **Target** | Executive user |
| **Defender Severity** | Low |
| **User Action** | Email read + phishing link clicked |
| **Credentials Submitted** | No |
| **Decision** | Escalated for further investigation |

### 1. Phishing Email

The user received a simulated quarantine notification containing a **Release Message** link designed to represent a credential-harvesting attempt.

![Phishing Email](screenshots/01-phishing-email-received.png)

### 2. User Activity

Attack Simulation Training confirmed:

- Message delivered: **1/1**
- Message read: **1/1**
- Link clicked: **1/1**
- Credentials supplied: **0/1**
- Compromised users: **0/1**

![Attack Simulation Activity](screenshots/02-attack-simulation-user-activity.png)

### 3. Defender Incident

Microsoft Defender XDR correlated the activity into an incident categorized as **Credential Phish** involving one user.

I retained Defender's **Low** severity rather than manually increasing it based on the user's executive role.

![Defender Incident](screenshots/03-defender-credential-phish-incident.png)

### 4. KQL Scope Check

I used Advanced Hunting to review recent email activity associated with the affected mailbox:

```kql
EmailEvents
| where Timestamp > ago(2d)
| where RecipientEmailAddress =~ "Michael.Weber@PatilCyberSolutionsGmbH.onmicrosoft.com"
| project Timestamp,
          SenderFromAddress,
          SenderFromDomain,
          RecipientEmailAddress,
          Subject,
          ThreatTypes,
          DetectionMethods,
          DeliveryAction,
          DeliveryLocation,
          NetworkMessageId
| order by Timestamp desc
```

The query identified related Attack Simulation Training email telemetry and provided the `NetworkMessageId` for potential deeper investigation.

![Advanced Hunting](screenshots/04-advanced-hunting-email-scope.png)

## Analyst Decision

**Verdict: True Positive / Suspicious Activity — Escalated**

The phishing activity and link interaction were confirmed, but there was **no evidence of credential submission or confirmed account compromise**.

Because credential phishing involved a high-value executive account and the user interacted with the link, I would escalate the incident to Tier-2 for deeper identity and mailbox investigation rather than declaring the account compromised at Tier-1.

## Skills Demonstrated

`Microsoft Defender XDR` · `Defender for Office 365` · `Phishing Investigation` · `KQL` · `Advanced Hunting` · `Email Analysis` · `Incident Triage` · `Scope Analysis` · `SOC Escalation`

> **Lab Note:** This incident was generated in an authorized Microsoft security lab using Microsoft Defender Attack Simulation Training. All activity shown is controlled simulation data.
