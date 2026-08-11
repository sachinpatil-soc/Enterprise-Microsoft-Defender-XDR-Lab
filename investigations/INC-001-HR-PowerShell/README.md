# 🚨 Incident 001 – Suspicious PowerShell Activity (Human Resources)

## 📌 Incident Overview

This incident documents the investigation of a **Medium-severity Suspicious PowerShell Command Line alert** detected by Microsoft Defender for Endpoint within the Human Resources user context.

The activity contained several characteristics commonly associated with malicious PowerShell execution, including **ExecutionPolicy Bypass**, a **hidden PowerShell window**, file-download behavior, and execution of a downloaded artifact.

Rather than classifying the activity based solely on the alert title or process name, the investigation used **process-tree analysis, command-line inspection, PID correlation, endpoint telemetry, Advanced Hunting, and tenant-wide scoping** to determine the execution context and appropriate SOC disposition.

---

## 📋 Incident Summary

| Field | Value |
|---|---|
| **Incident ID** | `INC-001` |
| **Department** | Human Resources |
| **User Context** | Emma Wilson (HR) |
| **Endpoint** | `SP-SOC-LAB-TIER-01` |
| **Alert** | Suspicious PowerShell Command Line |
| **Severity** | Medium |
| **Detection Source** | Microsoft Defender for Endpoint |
| **Investigation Platform** | Microsoft Defender XDR |
| **Investigation Method** | Alert Triage + Process Analysis + Advanced Hunting |
| **Final Classification** | Expected / Authorized Security Validation Activity |
| **Disposition** | Resolved – No Escalation Required |

---

# 🎯 Investigation Objective

The objective of this investigation was to validate Microsoft Defender for Endpoint telemetry while practicing a structured Tier-1 SOC investigation.

The investigation was designed to answer the following questions:

1. 🔍 What triggered the alert?
2. 👤 Which user executed the activity?
3. 💻 Which endpoint was involved?
4. 🌳 What was the parent-child process relationship?
5. ⚙️ What commands were executed?
6. 🕒 What occurred before and after the detection?
7. 🌍 Was the activity observed elsewhere in the environment?
8. 🚨 Should the alert be escalated or resolved?

---

# 1️⃣ Initial Alert Triage

Microsoft Defender for Endpoint generated a **Medium-severity** alert:

> **Suspicious PowerShell command line**

The alert was associated with the Human Resources user context and required investigation because the PowerShell execution contained several security-relevant characteristics.

### 🔎 Initial Indicators

The command included:

```text
-ExecutionPolicy Bypass
-WindowStyle Hidden
System.Net.WebClient
DownloadFile()
Start-Process()
```

These behaviors can appear in legitimate administrative automation, but they are also frequently relevant during investigations of malicious PowerShell activity.

The alert was therefore treated as **suspicious pending investigation** rather than immediately classified as either malicious or benign.

### 📸 Evidence – Defender Alert

![Microsoft Defender Suspicious PowerShell Alert](screenshots/01-defender-alert.png)

### 🧠 Analyst Observation

PowerShell itself is not inherently malicious.

However, the combination of:

- Execution-policy bypass
- Hidden execution
- File-download behavior
- Subsequent execution

increased the level of suspicion and justified further investigation.

---

# 2️⃣ Process Tree Investigation

The Microsoft Defender **Alert Story** was reviewed to identify the execution relationship associated with the suspicious PowerShell activity.

The relevant relationship showed:

```text
cmd.exe
   │
   └── powershell.exe
          │
          ├── -ExecutionPolicy Bypass
          ├── -WindowStyle Hidden
          ├── DownloadFile()
          └── Start-Process()
```

The PowerShell command referenced:

```text
http://127.0.0.1/1.exe
```

and attempted to write the artifact to:

```text
C:\test-WDATP-test\invoice.exe
```

### 📸 Evidence – Process Tree and Command Line

![Defender Process Tree](screenshots/02-process-tree-command-line.png)

### 🔎 Analyst Observation

The process tree established that:

```text
InitiatingProcessFileName = cmd.exe
FileName                  = powershell.exe
```

Therefore:

```text
cmd.exe            ← Parent / initiating process
   │
   └── powershell.exe    ← Child / observed process
```

This was important because the investigation focused not only on **what process executed**, but also **what initiated it and with which arguments**.

---

# 3️⃣ Command-Line Analysis

The PowerShell command line contained multiple behaviors requiring validation.

Relevant components included:

```text
powershell.exe
-NoExit
-ExecutionPolicy Bypass
-WindowStyle Hidden
```

along with:

```text
System.Net.WebClient().DownloadFile(...)
```

The command referenced:

```text
http://127.0.0.1/1.exe
```

and:

```text
C:\test-WDATP-test\invoice.exe
```

### ⚠️ Why This Was Suspicious

From a SOC perspective, the investigation could not assume the activity was safe simply because PowerShell is a legitimate Windows component.

A legitimate Windows binary can be abused to perform malicious actions.

Likewise, a legitimate-looking filename is not proof that a file is trustworthy.

Therefore, the investigation continued into raw endpoint telemetry.

---

# 4️⃣ Advanced Hunting Investigation

Microsoft Defender **Advanced Hunting** was used to examine endpoint process telemetry surrounding the detection.

The initial hunting window examined activity shortly before and after the alert in order to separate relevant execution events from normal Windows background activity.

### 🔍 Investigation Fields

The following telemetry was particularly important:

```text
Timestamp
DeviceName
AccountName
FileName
ProcessCommandLine
InitiatingProcessFileName
InitiatingProcessCommandLine
ProcessId
InitiatingProcessId
SHA1
```

Advanced Hunting allowed the alert evidence to be independently correlated against endpoint telemetry.

---

## 🔎 Relevant Process Filtering

Instead of treating every Windows process in the time window as suspicious, the investigation narrowed the results to processes and indicators relevant to the alert.

This reduced background noise and made the execution relationship easier to reconstruct.

### 📸 Evidence – Advanced Hunting

![Advanced Hunting Process Correlation](screenshots/03-advanced-hunting-process-correlation.png)

---

# 5️⃣ Process ID Correlation

Process IDs were used to correlate the specific process instances involved in the detection.

The PowerShell event showed:

```text
FileName: powershell.exe
ProcessId: 3484

InitiatingProcessFileName: cmd.exe
InitiatingProcessId: 9744
```

A corresponding `cmd.exe` process was observed with:

```text
ProcessId: 9744
```

The matching process identifiers supported the relationship:

```text
cmd.exe
PID 9744
     │
     └── powershell.exe
         PID 3484
```

### 🧠 Why PID Correlation Matters

Multiple instances of processes such as `cmd.exe` or `powershell.exe` may execute on the same endpoint.

Process names alone therefore do not uniquely identify a specific process instance.

The relationship:

```text
cmd.exe ProcessId = 9744
              ↕
PowerShell InitiatingProcessId = 9744
```

helped correlate the relevant parent process with the observed PowerShell execution.

> **Note:** Process IDs are dynamically assigned and can be reused. PID correlation was therefore evaluated together with the device, timestamps, process names, command lines, and surrounding telemetry.

---

# 6️⃣ Execution Context Validation

Advanced Hunting also identified command-shell activity associated with the controlled Defender lab workflow.

One observed command was:

```text
cmd.exe /c "Z:\WindowsDefenderATPLocalOnboardingScript.cmd"
```

In this command:

```text
/c
```

instructs `cmd.exe` to execute the specified command and then terminate.

The referenced script was located on:

```text
Z:\
```

which was the shared-drive location used within the lab environment.

### ⚠️ Analyst Validation Principle

The filename:

```text
WindowsDefenderATPLocalOnboardingScript.cmd
```

was **not considered sufficient evidence by itself** to classify the activity as benign.

An attacker can rename malicious files to resemble legitimate software or administrative scripts.

The filename therefore served only as **contextual evidence**.

The final determination required correlation with:

- Known authorized lab activity
- Process relationships
- Command-line arguments
- User context
- Endpoint telemetry
- Timestamps
- Scope analysis

---

# 7️⃣ Timeline Reconstruction

The investigation reconstructed the relevant execution sequence using endpoint telemetry rather than relying solely on the original alert.

Observed relationships included:

```text
explorer.exe
     │
     └── cmd.exe
          │
          └── Defender lab workflow
```

and the suspicious detection chain:

```text
cmd.exe [PID 9744]
     │
     └── powershell.exe [PID 3484]
             │
             ├── ExecutionPolicy Bypass
             ├── Hidden Window
             ├── DownloadFile()
             └── invoice.exe
```

### 🧠 Analyst Interpretation

The objective of process-tree analysis was not simply to identify the final executable.

Instead, the goal was to reconstruct enough of the execution chain to understand:

- How execution began
- Which processes were involved
- Which user initiated the activity
- What commands were executed
- What artifacts were referenced
- Whether additional suspicious behavior followed

---

# 8️⃣ Scope Investigation

After understanding the activity on the original endpoint, the investigation expanded from **event analysis** to **environment-wide scoping**.

The objective was to determine whether the same indicators were observed on additional endpoints or under additional user accounts.

### 🔎 Scope Indicators

The investigation pivoted on distinctive indicators including:

```text
invoice.exe
test-WDATP-test
127.0.0.1/1.exe
```

The original endpoint restriction was intentionally removed so the indicators could be searched across available Defender endpoint telemetry.

### KQL – Tenant-Wide Scope Hunt

```kql
DeviceProcessEvents
| where Timestamp between (
    datetime(2026-08-03 15:55:00) ..
    datetime(2026-08-03 16:15:00)
)
| where FileName =~ "powershell.exe"
| where ProcessCommandLine has_any (
    "test-WDATP-test",
    "invoice.exe",
    "127.0.0.1/1.exe"
)
| project
    Timestamp,
    DeviceName,
    AccountName,
    FileName,
    ProcessCommandLine,
    InitiatingProcessFileName,
    ProcessId,
    InitiatingProcessId,
    SHA1
| order by Timestamp asc
```

### KQL – Scope Summary

```kql
DeviceProcessEvents
| where Timestamp between (
    datetime(2026-08-03 15:55:00) ..
    datetime(2026-08-03 16:15:00)
)
| where ProcessCommandLine has_any (
    "invoice.exe",
    "test-WDATP-test",
    "127.0.0.1/1.exe"
)
| summarize
    EventCount=count(),
    FirstSeen=min(Timestamp),
    LastSeen=max(Timestamp)
    by DeviceName, AccountName
| order by EventCount desc
```

### 📸 Evidence – Scope Validation

![Tenant-Wide Scope Hunt](screenshots/04-scope-validation.png)

### 🌍 Scope Result

> **UPDATE AFTER RUNNING THE SCOPE QUERY**

The final scope result will document:

- Number of affected endpoints
- Number of affected users
- Whether the indicators appeared elsewhere
- Whether lateral movement or broader propagation required investigation

---

# 9️⃣ Evidence Summary

| Evidence | Finding |
|---|---|
| 👤 **User** | Emma Wilson (HR) |
| 💻 **Endpoint** | `SP-SOC-LAB-TIER-01` |
| 🚨 **Alert** | Suspicious PowerShell Command Line |
| ⚙️ **Observed Process** | `powershell.exe` |
| 🌳 **Initiating Process** | `cmd.exe` |
| 🆔 **PowerShell PID** | `3484` |
| 🔗 **Initiating PID** | `9744` |
| ⚠️ **Execution Policy** | `Bypass` |
| 🫥 **Window Style** | `Hidden` |
| 📥 **Download Method** | `System.Net.WebClient().DownloadFile()` |
| 🌐 **Referenced URL** | `http://127.0.0.1/1.exe` |
| 📁 **Referenced Artifact** | `C:\test-WDATP-test\invoice.exe` |
| 🛡️ **Detection Source** | Microsoft Defender for Endpoint |
| 🌍 **Scope** | Pending final scope-hunt result |

---

# ⚔️ MITRE ATT&CK Mapping

| Technique | Name | Observed Behavior |
|---|---|---|
| **T1059.001** | PowerShell | PowerShell used for scripted command execution |
| **T1105** | Ingress Tool Transfer | File-transfer behavior observed through `DownloadFile()` |

> MITRE ATT&CK mappings describe the observed techniques and do not by themselves determine whether the activity was malicious.

---

# 🧠 Final Analyst Assessment

The original detection contained several characteristics that warranted investigation:

- Hidden PowerShell execution
- Execution-policy bypass
- File-download behavior
- Execution of a referenced artifact
- Command-shell initiated PowerShell

The activity was therefore not dismissed solely because it occurred within a controlled lab.

The investigation correlated:

- Alert metadata
- Parent-child process relationships
- Command-line telemetry
- Process IDs
- User context
- Endpoint context
- Surrounding process activity
- Known authorized activity
- Environment-wide scope

The collected evidence was consistent with the controlled Microsoft Defender security validation activity performed within the lab environment.

**The final scope statement will be updated after completion of the tenant-wide hunting query.**

---

# ✅ Final Decision

## Classification

**Expected / Authorized Security Validation Activity**

## Disposition

**Resolved – No Escalation Required**

## Rationale

Although the PowerShell execution exhibited several behaviors commonly associated with malicious activity, the surrounding telemetry and known execution context correlated with the authorized Microsoft Defender validation exercise.

The activity was therefore determined to be expected security-testing activity rather than evidence of unauthorized compromise.

> Final closure should be supported by the completed scope investigation confirming whether the relevant indicators were isolated to the expected endpoint/user context.

---

# 🎓 Lessons Learned

- 🔍 **An alert begins an investigation; it does not determine the verdict.**
- 🌳 **Parent-child process relationships help reconstruct execution behavior.**
- 🆔 **Process IDs help correlate specific process instances within the relevant device and time context.**
- 💻 **PowerShell is not inherently malicious; behavior and context determine risk.**
- ⚠️ **Legitimate-looking filenames are contextual evidence, not proof of legitimacy.**
- 🕒 **Reviewing activity before and after a detection provides critical execution context.**
- 🔬 **Advanced Hunting can validate and extend evidence presented in the Defender alert.**
- 🌍 **Scope must be established before confidently closing suspicious activity.**
- 🧩 **Strong indicators should be pivoted across the environment rather than investigated only on the original endpoint.**
- 🚨 **Escalation decisions should be based on correlated evidence rather than assumptions.**

---

# 🛠️ Skills Demonstrated

This investigation demonstrates practical experience with:

- 🛡️ Microsoft Defender for Endpoint
- 🔍 Microsoft Defender XDR alert triage
- 🌳 Parent-child process analysis
- ⚙️ Windows process investigation
- 🧠 Command-line analysis
- 🆔 PID correlation
- 🕒 Timeline reconstruction
- 🔬 Microsoft Defender Advanced Hunting
- 📊 KQL investigation queries
- 🌍 Environment-wide IOC scoping
- ⚔️ MITRE ATT&CK mapping
- 📝 SOC incident documentation
- ⚖️ Evidence-based alert disposition

---

## 📁 Incident Evidence

```text
INC-001-Suspicious-PowerShell-HR/
│
├── README.md
│
└── screenshots/
    ├── 01-defender-alert.png
    ├── 02-process-tree-command-line.png
    ├── 03-advanced-hunting-process-correlation.png
    └── 04-scope-validation.png
```

---

## 🏁 Investigation Status

**INC-001: Awaiting final scope validation before closure documentation is considered complete.**
