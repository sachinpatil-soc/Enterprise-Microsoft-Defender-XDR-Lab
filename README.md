
# 🛡️ Enterprise-Microsoft-Defender-XDR-Lab

## 📌 Project Overview

This project demonstrates the design, deployment, and operation of a simulated enterprise Security Operations Center (SOC) using **Microsoft Defender XDR**. 

The objective of this lab is to gain practical, hands-on experience with enterprise endpoint security, Microsoft Defender for Endpoint (MDE), incident investigation, and SOC workflows that closely resemble a live production environment. Rather than focusing on isolated demonstrations, this lab simulates a small, functional organization featuring multiple departments, business users, and Windows endpoints.

---

## 🏗️ Architecture Diagram 

 ``` mermaid
graph TD
    %% Define Styles
    classDef host fill:#2d3748,stroke:#1a202c,stroke-width:2px,color:#fff;
    classDef hyper fill:#4a5568,stroke:#2d3748,stroke-width:2px,color:#fff;
    classDef endpoint fill:#3182ce,stroke:#2b6cb0,stroke-width:2px,color:#fff;
    classDef security fill:#e53e3e,stroke:#c53030,stroke-width:2px,color:#fff;
    classDef identity fill:#dd6b20,stroke:#c05621,stroke-width:2px,color:#fff;
    classDef soc fill:#319795,stroke:#2c7a7b,stroke-width:2px,color:#fff;

    %% Nodes
    A[💻 MacBook Air Host]:::host
    B[⚙️ UTM Hypervisor]:::hyper
    C[🖥️ Windows 11 Enterprise VMs]:::endpoint
    D[🛡️ Microsoft Defender for Endpoint]:::security
    E[☁️ Microsoft Defender XDR Portal]:::security
    F[🆔 Microsoft Entra ID]:::identity
    G[🔍 SOC Analyst Investigation]:::soc

    %% Flow/Connections
    A --> B
    B --> C
    C --> D
    D --> E
    F --> E
    E --> G

    %% Environments
    subgraph Local Environment
    A
    B
    C
    end

    subgraph Microsoft 365 Cloud Enterprise Tenant
    D
    E
    F
    end


 ``` 




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

## ⚔️ MITRE ATT&CK Mapping

The threat simulations executed within this lab environment map directly to the following industry-standard **MITRE ATT&CK** tactics and techniques:

* 📨 **T1566 – Phishing:** Initial access simulation via malicious email attachments or links delivered to target endpoints.
* 💻 **T1059.001 – Command and Scripting Interpreter (PowerShell):** Execution of secondary discovery payloads and obfuscated automation commands.
* 🛡️ **T1218 – Signed Binary Proxy Execution:** Defense evasion utilizing trusted Windows system binaries to bypass local security controls.
* 📥 **T1105 – Ingress Tool Transfer:** Transfer of external tools, malware artifacts, or utilities into the local system environment.
* 💉 **T1055 – Process Injection (Planned):** Evasion and persistence via running malicious code within the address space of a legitimate process.


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



