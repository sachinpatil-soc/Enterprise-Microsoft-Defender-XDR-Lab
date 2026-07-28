
# 🛡️ Enterprise-Microsoft-Defender-XDR-Lab

## 📌 Project Overview

This project demonstrates the design, deployment, and operation of a simulated enterprise Security Operations Center (SOC) using **Microsoft Defender XDR**. 

The objective of this lab is to gain practical, hands-on experience with enterprise endpoint security, Microsoft Defender for Endpoint (MDE), incident investigation, and SOC workflows that closely resemble a live production environment. Rather than focusing on isolated demonstrations, this lab simulates a small, functional organization featuring multiple departments, business users, and Windows endpoints.

---

## 🎯 Lab Objectives
* 🏗️ **Build an Infrastructure:** Provision a Microsoft 365 enterprise tenant from scratch.
* 👥 **Identity Management:** Configure Microsoft Entra ID users, groups, and administrative roles.
* 🛡️ **Security Deployment:** Deploy and tune Microsoft Defender XDR across the enterprise.
* 💻 **Endpoint Onboarding:** Onboard multiple Windows endpoints via automated and manual methods.
* 🏢 **Simulate Business Operations:** Model realistic enterprise users and functional departments.
* 🚨 **Generate Threat Telemetry:** Execute simulation scripts to trigger realistic security alerts.
* 🔍 **Triage & Investigate:** Analyze cross-domain incidents using the Microsoft Defender Portal.
* 📝 **Incident Documentation:** Document findings and timelines using professional SOC reporting templates.
* ⚡ **Advanced Threat Hunting:** Author and execute KQL (Kusto Query Language) queries to hunt for IOCs.

---

## 🏢 Enterprise Environment Map

| Department | User Name | Device Hostname | Status |
| :--- | :--- | :--- | :--- |
| **Human Resources** | Emma Wilson | `HR-LAPTOP-01` | 🟢 Active / Onboarded |
| **Sales** | Anna Becker | `SALES-LAPTOP-01` | 🟢 Active / Onboarded |
| **Finance** | John Schneider | `FIN-LAPTOP-01` | ⏳ Planned |
| **Executive** | Michael Weber | `EXEC-LAPTOP-01` | ⏳ Planned |
| **IT Administration** | Alex Müller | `MGMT-WORKSTATION` | ⏳ Planned |
| **Security Operations** | Sachin Patil | `SOC-ANALYST-01` | 💻 Defender Portal Access |

---

## 🛠️ Technologies Used
* **SIEM/XDR:** Microsoft Defender XDR Portal
* **EDR/EPP:** Microsoft Defender for Endpoint (MDE)
* **Identity & Access:** Microsoft Entra ID (Azure AD)
* **Cloud Suite:** Microsoft 365 Enterprise
* **Operating Systems:** Windows 11 Enterprise
* **Hypervisor:** UTM Virtual Machines (Apple Silicon Host)
* **Automation:** PowerShell
* **Query Language:** KQL (Kusto Query Language)

---

## 🧠 Skills Demonstrated (SOC Tier-1 Alignment)
* 📥 **Endpoint Provisioning:** Manual and script-based onboarding configuration.
* 🚦 **Alert Triage:** Analyzing severity, scoping impact, and determining true vs. false positives.
* 🕵️‍♂️ **Incident Investigation:** Deep-dives into telemetry logs, artifacts, and network connections.
* 🌿 **Process Tree Analysis:** Evaluating parent-child process relationships (e.g., `cmd.exe` spawned by `wscript.exe`).
* 🕒 **Timeline Analysis:** Chronologically mapping adversary behavior from initial access to execution.
* 🏷️ **IOC Collection:** Extracting file hashes (SHA256), malicious IPs, and rogue registry keys.
* 📊 **Evidence-Based Reporting:** Writing concise executive summaries and detailed technical metrics.

---

## 📈 Current Project Progress

- [x] Create and configure Microsoft 365 enterprise tenant
- [x] Deploy and initialize Microsoft Defender XDR licensing
- [x] Provision Entra ID enterprise users and assign security groups
- [x] Onboard `HR-LAPTOP-01` to Microsoft Defender for Endpoint
- [x] Onboard `SALES-LAPTOP-01` to Microsoft Defender for Endpoint
- [x] Standardize SOC incident investigation templates and triage workflow
- [ ] Onboard remaining departmental endpoints (Finance, Executive, IT)
- [ ] Execute attack simulations and document complete incident lifecycles
- [ ] Build a custom KQL threat hunting repository



