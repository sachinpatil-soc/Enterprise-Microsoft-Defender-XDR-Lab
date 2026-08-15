# INC-003 — CEO Phishing Investigation

> **Microsoft Defender XDR | Tier-1 SOC Investigation | Executive User**


## 📋 Incident Summary

| Field | Finding |
|---|---|
| 👤 User | Michael Weber — Chief Executive Officer |
| 💻 Endpoint | `sp-soc-lab-tier-01` |
| 🛡️ Platform | Microsoft Defender XDR |
| ⚠️ Final Severity | Medium |
| 📌 Final Status | Active |
| 🏷️ Classification | True Positive — Multi-staged attack |
| 🔎 Activity | Suspicious PowerShell execution + endpoint discovery 
| 🚨 Tier-1 Decision | Escalated to Tier-2 |

---

## 🎯 Overview

INC-003 began with a controlled credential-phishing simulation targeting executive user **Michael Weber**.

Further investigation identified suspicious PowerShell execution and endpoint discovery activity on `sp-soc-lab-tier-01`. Microsoft Defender correlated the activity into a multi-stage incident involving **Execution** and **Discovery**.

Tier-1 classified the incident as a **True Positive — Multi-staged attack** and escalated it to Tier-2 for deeper investigation.

---

## 1️⃣ Phishing Email

The user received a simulated quarantine notification containing a **Release Message** link designed to represent a credential-harvesting attempt.

### 📸 Evidence 01 — Phishing Email

![Phishing Email](https://github.com/sachinpatil-soc/Enterprise-Microsoft-Defender-XDR-Lab/blob/d0f9149d544992d2d868a78886755673b5b25690/investigations/INC-003-CEO%20Phishing%20Investigation/01-phishing-email-received.png)

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

The alert provided the initial Defender signal associated with the reported phishing activity.

### 📸 Evidence 03 — Defender Alert

![Defender Alert](https://github.com/sachinpatil-soc/Enterprise-Microsoft-Defender-XDR-Lab/blob/13dd72bf2a4d43ca877b812b129c72a9e4c4a6cc/investigations/INC-003-CEO%20Phishing%20Investigation/03-defender-alert-email-reported-phish.png)

**Observation:**  
The phishing alert established the initial security signal for the investigation. Tier-1 continued reviewing the affected user and related telemetry to determine whether additional suspicious activity was present.


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



## 5️⃣ Tier-1 Escalation Summary

Further investigation identified suspicious PowerShell execution on `sp-soc-lab-tier-01`, including:

- Execution-policy bypass
- Hidden PowerShell execution
- Attempted executable download/start
- Subsequent `whoami`, `hostname`, and `ipconfig /all` discovery activity

Microsoft Defender correlated the **Execution** and **Discovery** activity into the same Medium-severity incident.

### 📸 Evidence 05 — Tier-1 Escalation Summary


![Tier-1 Escalation Summary](https://github.com/sachinpatil-soc/Enterprise-Microsoft-Defender-XDR-Lab/blob/0b0b4b96e4b1c979ebf9260fca09000175729207/investigations/INC-003-CEO%20Phishing%20Investigation/05-tier1-escalation-summary.png)

**Observation:**  
The correlated PowerShell and discovery activity provided sufficient evidence for Tier-1 escalation. Successful payload execution or account compromise was not confirmed during Tier-1 analysis, so the incident remained **Active** pending further investigation.

---

## 🚨 Final Tier-1 Decision

**True Positive — Multi-staged attack → Escalated to Tier-2**

The escalation was based on the combination of suspicious PowerShell execution and subsequent endpoint discovery activity, not solely on the user's executive role.

A **Tier-2 Investigation Required** task was created for deeper validation.

---

## 🎓 SOC Skills Demonstrated

`Alert Triage` · `Microsoft Defender XDR` · `PowerShell Analysis` · `KQL` · `Advanced Hunting` · `Custom Detection` · `Incident Correlation` · `Tier-1 Escalation`

---

> **Lab Note:** This investigation was performed in an authorized Microsoft Defender security lab using controlled Attack Simulation Training, endpoint test activity, and custom detection telemetry.
