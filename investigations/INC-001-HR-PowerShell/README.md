# 🚨 INC-001 — Suspicious PowerShell Activity

> **Microsoft Defender XDR | Tier-1 SOC Investigation | Human Resources**

## 📋 Incident Summary

| Field | Finding |
|---|---|
| 🚨 Alert | Suspicious PowerShell Command Line |
| ⚠️ Severity | Medium |
| 👤 User | Emma Wilson (HR) |
| 💻 Endpoint | `SP-SOC-LAB-TIER-01` |
| 🛡️ Detection | Microsoft Defender for Endpoint |
| 🔎 Investigation | Process Tree + Command Line + KQL + Scope |
| ✅ Decision | Expected / Authorized Activity — Resolved |

---

## 🎯 Incident Overview

Microsoft Defender generated a **Medium-severity alert** after detecting PowerShell execution containing several behaviors commonly associated with malicious activity:

- `ExecutionPolicy Bypass`
- `WindowStyle Hidden`
- `DownloadFile()`
- `Start-Process()`

Because legitimate administrative activity can produce similar telemetry, the alert was investigated before classification.

### 📸 Evidence 01 — Defender Alert

![Defender alert overview](https://github.com/sachinpatil-soc/Enterprise-Microsoft-Defender-XDR-Lab/blob/e90cc1f798ec8cfe16427cc8b8d7d140add669ef/investigations/INC-001-HR-PowerShell/01-alert-overview.png)

**Analyst observation:**  
The alert established the affected endpoint, user, severity, detection source, and suspicious PowerShell execution requiring investigation.

---

## 🌳 Process & Command-Line Analysis

Defender's process telemetry identified the execution relationship:

```text
cmd.exe
   │
   └── powershell.exe
          │
          ├── ExecutionPolicy Bypass
          ├── WindowStyle Hidden
          ├── DownloadFile()
          └── Start-Process()
```

The PowerShell command referenced:

```text
http://127.0.0.1/1.exe
```

and attempted to write the file to:

```text
C:\test-WDATP-test\invoice.exe
```

### 📸 Evidence 02 — Process Tree & Command Line

![Process Tree and Command Line](https://github.com/sachinpatil-soc/Enterprise-Microsoft-Defender-XDR-Lab/blob/e90cc1f798ec8cfe16427cc8b8d7d140add669ef/investigations/INC-001-HR-PowerShell/02-process-chain.png)

**Analyst observation:**  
The combination of hidden PowerShell execution, execution-policy bypass, file download, and subsequent execution justified further investigation. The process tree alone was **not sufficient to determine maliciousness**.

---

## 🔬 Advanced Hunting & PID Correlation

Microsoft Defender Advanced Hunting was used to validate the activity using raw endpoint telemetry.

The PowerShell event showed:

```text
FileName:               powershell.exe
ProcessId:              3484
InitiatingProcess:      cmd.exe
InitiatingProcessId:    9744
```

The corresponding `cmd.exe` event showed:

```text
FileName:               cmd.exe
ProcessId:              9744
```

This correlates the specific process instances:

```text
cmd.exe [PID 9744]
        │
        └── powershell.exe [PID 3484]
```

### 📸 Evidence 03 — Advanced Hunting Correlation

![Advanced Hunting PID Correlation](https://github.com/sachinpatil-soc/Enterprise-Microsoft-Defender-XDR-Lab/blob/a5cb4212741ce55799951dc5e7f1752e66972144/investigations/INC-001-HR-PowerShell/03-timeline-investigation.png)

**Analyst observation:**  
Matching `powershell.exe`'s `InitiatingProcessId` with the `ProcessId` of `cmd.exe` provided telemetry-based evidence of the parent-child relationship rather than relying only on process names.

---

## 🌍 Scope Investigation

The identified command-line indicators were hunted across available Microsoft Defender endpoint telemetry.

```kql
DeviceProcessEvents
| where Timestamp > ago(30d)
| where ProcessCommandLine has_any (
    "test-WDATP-test",
    "invoice.exe",
    "127.0.0.1/1.exe"
)
| summarize
    EventCount=count(),
    FirstSeen=min(Timestamp),
    LastSeen=max(Timestamp)
    by DeviceName, AccountName
| order by EventCount desc
```

### 📸 Evidence 04 — Scope Analysis

![KQL Scope Analysis](https://github.com/sachinpatil-soc/Enterprise-Microsoft-Defender-XDR-Lab/blob/a5cb4212741ce55799951dc5e7f1752e66972144/investigations/INC-001-HR-PowerShell/04-kql-scope-hunt.png)

The hunt returned two isolated historical lab occurrences:

| Endpoint | User | Events |
|---|---|---:|
| `HR-LAPTOP-01` | Sachin | 1 |
| `SP-SOC-LAB-TIER-01` | Emma Wilson (HR) | 1 |

`HR-LAPTOP-01` represents the previous lab endpoint used during earlier Defender validation. The current investigation involved `SP-SOC-LAB-TIER-01`.

No repeated execution or additional suspicious propagation was identified within the available Defender telemetry.

---

## 🧠 Investigation Decision

The initial PowerShell behavior was suspicious and warranted investigation.

However, correlation of:

- alert context,
- parent-child process relationship,
- PowerShell command line,
- PID relationships,
- endpoint telemetry,
- known authorized lab activity, and
- environment-wide scope

supported the conclusion that the activity originated from an **authorized Microsoft Defender validation exercise** rather than an uncontrolled malicious execution.

---

## ⚔️ MITRE ATT&CK Mapping

| Technique | Description |
|---|---|
| `T1059.001` | PowerShell |
| `T1105` | Ingress Tool Transfer |

> MITRE ATT&CK techniques describe observed behavior; they do not by themselves prove malicious intent.

---

## ✅ Final Disposition

| Decision | Result |
|---|---|
| **Classification** | Expected / Authorized Activity |
| **Escalation** | Not Required |
| **Containment** | Not Required |
| **Status** | Resolved |

### Reason for Resolution

The activity was traced to an authorized Defender validation workflow, the process relationship was validated through endpoint telemetry, and scope analysis identified no additional suspicious propagation within the available dataset.

---

## 🎓 SOC Skills Demonstrated

`Alert Triage` • `Process Tree Analysis` • `Command-Line Analysis` • `PID Correlation` • `KQL` • `Advanced Hunting` • `Scope Analysis` • `MITRE ATT&CK` • `Incident Disposition`

---

### 🛡️ Analyst Takeaway

> **An alert identifies behavior worth investigating — it does not determine the verdict. Context, correlation, and scope determine the final disposition.**
