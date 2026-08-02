# 🚨 Incident 001 – Suspicious PowerShell Activity (Human Resources)

## 📋 Incident Summary

| Field | Value |
|-------|-------|
| Incident ID | INC-001 |
| Department | Human Resources |
| User | Sachin *(Lab Validation User)* |
| Device | HR-LAPTOP-01 |
| Alert | Suspicious PowerShell Command Line |
| Severity | Medium |
| Detection Source | Microsoft Defender for Endpoint |
| Status | Resolved (Expected Administrative Activity) |

---

# 🎯 Objective

Validate Microsoft Defender for Endpoint onboarding and practice a complete Tier-1 SOC investigation using a simulated PowerShell execution.

---

# 🧠 Initial Assessment

Microsoft Defender generated a Medium severity alert after detecting a suspicious PowerShell command.

The command downloaded and executed a test executable using PowerShell with:

- ExecutionPolicy Bypass
- Hidden Window
- DownloadFile()
- Start-Process()

Although these behaviors commonly appear in malicious activity, they are also consistent with Microsoft's official MDE onboarding validation script.

---

# 🔍 Investigation Plan

1. Review alert metadata.
2. Analyze process tree.
3. Inspect PowerShell command line.
4. Review device timeline.
5. Identify user and device.
6. Collect evidence (command line, hashes, file path, network indicators).
7. Determine scope.
8. Validate whether the activity is expected or malicious.
9. Document findings.

---

# 🌳 Process Tree

```text
cmd.exe
   │
   └── powershell.exe
            │
            ├── DownloadFile()
            └── invoice.exe
```

---

# 📂 Evidence Collected

## Device

```
HR-LAPTOP-01
```

## User

```
Sachin
```

## PowerShell

```
ExecutionPolicy Bypass
WindowStyle Hidden
DownloadFile()
Start-Process()
```

## Download URL

```
http://127.0.0.1/1.exe
```

## Download Location

```
C:\test-WDATP-test\invoice.exe
```

## Parent Process

```
cmd.exe
```

## Child Process

```
powershell.exe
```

---

# 🌍 Scope

Affected Devices

- HR-LAPTOP-01

Affected Users

- Sachin

Observed Spread

- None

Lateral Movement

- Not observed

---

# ⚔️ MITRE ATT&CK

| Technique | Description |
|-----------|-------------|
| T1059.001 | PowerShell |
| T1105 | Ingress Tool Transfer |

---

# 📝 Final Analysis

The investigation determined that the observed PowerShell activity was generated during a controlled Microsoft Defender onboarding validation exercise.

No persistence mechanisms, credential theft, privilege escalation, lateral movement, or additional malicious artifacts were identified.

The activity was limited to a single endpoint and matched the expected behavior of the test scenario.

---

# ✅ Final Decision

**Determination**

Expected Administrative Activity

**Reason**

Evidence supports that the activity originated from a controlled Microsoft Defender validation test rather than malicious execution.

No further containment or escalation was required.

---

# 🎓 Lessons Learned

- Parent-child process analysis is essential.
- Timeline analysis provides execution context.
- PowerShell alone is not malicious; context determines risk.
- Always verify scope before resolving an alert.
