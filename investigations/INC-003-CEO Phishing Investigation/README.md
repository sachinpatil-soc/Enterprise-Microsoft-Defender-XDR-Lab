# INC-003 — CEO Phishing Investigation

> **Microsoft Defender XDR | Tier-1 SOC Investigation | Executive User**

## 📋 Incident Summary

| Field | Finding |
|---|---|
| 🎣 Scenario | Credential Phishing |
| 👤 Target | Michael Weber — Chief Executive Officer |
| 🛡️ Platform | Microsoft Defender XDR / Defender for Office 365 |
| ⚠️ Defender Severity | Low |
| 📌 Defender Status | Resolved |
| 🏷️ Defender Classification | Unclassified |
| 👆 User Interaction | Email read + phishing link clicked |
| 🔐 Credentials Submitted | No |
| 🔎 Analyst Assessment | Phishing interaction confirmed; compromise not confirmed |
| 🚨 Analyst Recommendation | Escalate to Tier-2 for additional validation |

---

## 🎯 Overview

A controlled **Credential Harvest** phishing simulation targeted an executive user.

The Tier-1 investigation focused on validating the phishing interaction, reviewing the Defender alert and incident, checking available email telemetry, and determining whether additional investigation would be appropriate.

---

## 1️⃣ Phishing Email

The user received a simulated quarantine notification containing a **Release Message** link designed to represent a credential-harvesting attempt.

### 📸 Evidence 01 — Phishing Email

![Phishing Email](investigations/INC-003-CEO Phishing Investigation/01-phishing-email-received.png)

**Observation:**  
The email used a business-themed quarantine notification and prompted the executive user to interact with a link.

---

## 2️⃣ User Interaction

Microsoft Attack Simulation Training recorded:

- Message delivered: **1/1**
- Message read: **1/1**
- Link clicked: **1/1**
- Credentials supplied: **0/1**
- Compromised users: **0/1**

### 📸 Evidence 02 — Attack Simulation Activity

![Attack Simulation Activity](https://github.com/sachinpatil-soc/Enterprise-Microsoft-Defender-XDR-Lab/blob/96d9e1c7334383a0d86bcc078fc6b925e710230c/investigations/INC-003-CEO%20Phishing%20Investigation/03-defender-credential-phish-incident.png)

**Observation:**  
The phishing link was clicked, but the available simulation evidence showed **no credential submission and no confirmed compromise**.

---

## 3️⃣ Defender Alert

Microsoft Defender generated the alert:

**Email reported by user as malware or phish**

The alert showed:

- Severity: **Low**
- Status: **Resolved**
- Classification: **Not Set**
- Category: **Initial Access**

### 📸 Evidence 03 — Defender Alert

![Defender Alert](https://github.com/sachinpatil-soc/Enterprise-Microsoft-Defender-XDR-Lab/blob/96d9e1c7334383a0d86bcc078fc6b925e710230c/investigations/INC-003-CEO%20Phishing%20Investigation/02-attack-simulation-user-activity.png)

**Observation:**  
A genuine Defender alert was generated from the reported phishing workflow. The Defender-assigned severity was retained as **Low** rather than manually increasing it because the affected user was an executive.

---

## 4️⃣ Defender Incident

Microsoft Defender correlated the activity into:

**Email reported by user as malware or phish involving one user**

The incident showed:

- Severity: **Low**
- Status: **Resolved**
- Classification: **Unclassified**
- Category: **Credential Phish**
- Affected user: **Michael Weber**

### 📸 Evidence 04 — Credential Phish Incident

![Defender Incident](https://github.com/sachinpatil-soc/Enterprise-Microsoft-Defender-XDR-Lab/blob/96d9e1c7334383a0d86bcc078fc6b925e710230c/investigations/INC-003-CEO%20Phishing%20Investigation/04-advanced-hunting-email-scope.png)

**Observation:**  
The incident connected the reported phishing activity with the affected executive account. Defender had not classified the incident as a True Positive or confirmed account compromise.

---

## 5️⃣ Advanced Hunting Scope Check

A focused `EmailEvents` query was used to review recent email telemetry associated with the affected mailbox.

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

### 📸 Evidence 05 — Advanced Hunting

![Advanced Hunting](screenshots/05-advanced-hunting-email-scope.png)

The query returned one related Attack Simulation Training email event:

- Sender: `notification@attacksimulationtraining.com`
- Recipient: Michael Weber
- Subject: `Training assignment notification`
- Delivery action: `Delivered`
- Delivery location: `Inbox/folder`
- `NetworkMessageId` available for further pivoting

**Observation:**  
This query provided supporting mailbox telemetry. It did **not independently classify the returned email event as phishing**, so it was not used as proof of maliciousness.

---

## 🧠 Tier-1 Assessment

The available evidence confirmed:

- an executive user was targeted in a controlled credential-phishing simulation;
- the message was delivered and read;
- the phishing link was clicked;
- Defender generated a phishing-related alert and Credential Phish incident;
- no credential submission was recorded;
- no account compromise was confirmed during Tier-1 review.

The executive role increases the potential **business impact**, but it does not by itself determine alert severity or prove compromise.

---

## 🚨 Analyst Recommendation

**Recommend escalation to Tier-2 for additional identity and mailbox validation.**

The recommendation is based on the combination of:

- credential-phishing activity;
- confirmed user interaction with the phishing link; and
- the potential impact associated with a high-value executive account.

The Tier-1 investigation does **not** claim that credentials were stolen or that the account was compromised.

---

## 🎓 SOC Skills Demonstrated

`Alert Triage` · `Phishing Analysis` · `Microsoft Defender XDR` · `Defender for Office 365` · `KQL` · `Advanced Hunting` · `Scope Analysis` · `Evidence-Based Escalation`

---

> **Lab Note:** This incident was generated in an authorized Microsoft security lab using Microsoft Defender Attack Simulation Training. All activity shown is controlled simulation data.
