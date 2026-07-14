# 🛡️ Wazuh Threat Intelligence Integration with VirusTotal

![Wazuh](https://img.shields.io/badge/SIEM-Wazuh-blue)
![VirusTotal](https://img.shields.io/badge/Threat%20Intel-VirusTotal-red)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)
![License](https://img.shields.io/badge/License-MIT-yellow)

A hands-on SOC lab project demonstrating how to integrate **Wazuh SIEM** with the **VirusTotal Threat Intelligence Platform** to automatically detect and analyze suspicious file changes on an endpoint using **File Integrity Monitoring (FIM)**.

---

## 📌 Table of Contents

- [Project Overview](#-project-overview)
- [Objectives](#-objectives)
- [Lab Architecture](#️-lab-architecture)
- [Technologies Used](#️-technologies-used)
- [Prerequisites](#-prerequisites)
- [Step-by-Step Setup](#️-step-by-step-setup)
- [Testing the Integration](#-testing-the-integration)
- [Verifying Alerts](#-verifying-alerts)
- [Dashboard Verification](#-dashboard-verification)
- [Detection Flow Diagram](#-complete-detection-flow)
- [Implementation Status](#-final-implementation-status)
- [Security Benefits](#️-security-benefits)
- [Future Improvements](#-future-improvements)
- [Repository Structure](#-repository-structure)
- [Security Note (API Key)](#️-important-security-note)

---

## 📌 Project Overview

This project demonstrates the integration of **Wazuh SIEM** with the **VirusTotal Threat Intelligence Platform** to perform automated malware/hash reputation checking.

The system monitors file changes using **Wazuh File Integrity Monitoring (FIM)** and automatically sends file hashes to VirusTotal for threat analysis whenever a suspicious file modification occurs.

> 📸 **Screenshot placeholder:** Add a high-level overview screenshot of your lab setup (VMs, network diagram, or Wazuh dashboard home page) here.
> ```md
> ![Lab Overview](screenshots/lab-overview.png)
> ```

---

## 🎯 Objectives

- Deploy a Wazuh SIEM environment
- Connect a Windows endpoint with a Wazuh Agent
- Enable File Integrity Monitoring (FIM)
- Integrate the VirusTotal API
- Automatically analyze file hashes
- Generate security alerts
- Visualize threat intelligence results on the dashboard

---

## 🏗️ Lab Architecture

```
                 Internet
                    |
             VirusTotal API
                    |
+--------------------------------+
|         Ubuntu Server           |
|                                  |
|  Wazuh Manager                  |
|  Wazuh Analysis Engine          |
|  VirusTotal Integration         |
|                                  |
| IP: 10.29.94.131                |
+--------------------------------+
              |
          TCP 1514
              |
+--------------------------------+
|       Windows Endpoint          |
|                                  |
| Wazuh Agent                     |
| File Integrity Monitoring       |
|                                  |
| IP: 10.29.94.14                 |
+--------------------------------+
```

**Flow summary:**

```
Windows Endpoint → Wazuh Agent (FIM/Syscheck) → Wazuh Manager
      → VirusTotal API → Threat Intelligence Result → Wazuh Dashboard Alert
```

> 📸 **Screenshot placeholder:** Add a network topology diagram or draw.io export here.
> ```md
> ![Architecture Diagram](screenshots/architecture-diagram.png)
> ```

---

## 🛠️ Technologies Used

| Component | Purpose |
|---|---|
| Wazuh SIEM | Security monitoring and alerting |
| Wazuh Agent | Endpoint monitoring |
| Ubuntu Server | Wazuh Manager host |
| Windows 11 | Endpoint machine |
| VirusTotal API | Threat Intelligence lookup |
| Syscheck / FIM | File monitoring |

---

## ✅ Prerequisites

- A working Wazuh Manager installation on Ubuntu Server
- A Windows 11 endpoint with the Wazuh Agent installed and connected
- A free or paid [VirusTotal API key](https://www.virustotal.com/gui/join-us)
- Root/Administrator access on both machines

---

## ⚙️ Step-by-Step Setup

### Step 1 — Verify Wazuh Installation

Check Wazuh directories:

```bash
sudo ls /var/ossec/
```

Check integration scripts:

```bash
sudo ls /var/ossec/integrations/
```

**Expected output:**

```
virustotal
virustotal.py
```

> 📸 **Screenshot placeholder:** Terminal output showing the integrations folder.
> ```md
> ![Integrations Folder](screenshots/step1-integrations-folder.png)
> ```

---

### Step 2 — Configure VirusTotal Integration

Edit the Wazuh configuration file:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Add the following block inside `<ossec_config>`:

```xml
<integration>
  <name>virustotal</name>
  <api_key>YOUR_VIRUSTOTAL_API_KEY</api_key>
  <group>syscheck</group>
  <alert_format>json</alert_format>
</integration>
```

> 📸 **Screenshot placeholder:** `ossec.conf` file open in nano showing the integration block.
> ```md
> ![VirusTotal Config](screenshots/step2-virustotal-config.png)
> ```

---

### Step 3 — Validate Wazuh Configuration

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
```

**Expected:**

```
Configuration OK
```

> 📸 **Screenshot placeholder:** Terminal showing "Configuration OK".
> ```md
> ![Config Validation](screenshots/step3-config-validation.png)
> ```

---

### Step 4 — Restart Wazuh Manager

```bash
sudo systemctl restart wazuh-manager
```

Check status:

```bash
sudo systemctl status wazuh-manager
```

**Expected:**

```
active (running)
```

> 📸 **Screenshot placeholder:** Systemctl status output.
> ```md
> ![Manager Status](screenshots/step4-manager-status.png)
> ```

---

### Step 5 — Configure Windows Agent FIM

Open the agent configuration file:

```
C:\Program Files (x86)\ossec-agent\ossec.conf
```

Find the `<syscheck>` section and add the monitored directory:

```xml
<syscheck>

  <disabled>no</disabled>

  <directories realtime="yes">
    %PROGRAMDATA%\Microsoft\Windows\Start Menu\Programs\Startup
  </directories>

  <directories realtime="yes">
    C:\Users\Public
  </directories>

</syscheck>
```

> 📸 **Screenshot placeholder:** `ossec.conf` open on Windows showing the `<syscheck>` block.
> ```md
> ![Windows FIM Config](screenshots/step5-windows-fim-config.png)
> ```

---

### Step 6 — Restart Windows Agent

Open **PowerShell as Administrator**:

```powershell
Restart-Service WazuhSvc
```

Verify:

```powershell
Get-Service WazuhSvc
```

**Expected:**

```
Running
```

> 📸 **Screenshot placeholder:** PowerShell output showing the service running.
> ```md
> ![Agent Service Running](screenshots/step6-agent-service.png)
> ```

---

## 🧪 Testing the Integration

### Step 7 — Test File Integrity Monitoring

Create a test file:

```powershell
Set-Content C:\Users\Public\vt-test.txt "VirusTotal Test"
```

Modify the file:

```powershell
Set-Content C:\Users\Public\vt-test.txt "Modified Test"
```

> 📸 **Screenshot placeholder:** PowerShell commands creating/modifying the test file.
> ```md
> ![Test File Modification](screenshots/step7-test-file.png)
> ```

---

## 🔍 Verifying Alerts

### Step 8 — Verify Wazuh Alert

On the Ubuntu server:

```bash
sudo tail -f /var/ossec/logs/alerts/alerts.json
```

**Expected FIM Alert:**

```
"description":"Integrity checksum changed."
```

Example detail:

```
File 'c:\users\public\vt-test.txt' modified
Mode: realtime

Changed attributes:
md5
sha1
sha256
```

> 📸 **Screenshot placeholder:** `alerts.json` log output showing the FIM alert.
> ```md
> ![FIM Alert Log](screenshots/step8-fim-alert-log.png)
> ```

---

### Step 9 — VirusTotal Result

After FIM detection, Wazuh automatically sends the file hash to VirusTotal.

**Example alert:**

```
"description": "VirusTotal: Alert - No records in VirusTotal database"
"groups": ["virustotal"]
```

**Result:**

```json
{
  "found": "0",
  "malicious": "0"
}
```

**Meaning:**
- ✅ Hash submitted successfully
- ✅ No malicious record found

> 📸 **Screenshot placeholder:** Terminal or log output showing the VirusTotal alert.
> ```md
> ![VirusTotal Alert](screenshots/step9-virustotal-alert.png)
> ```

---

## 📊 Dashboard Verification

Open the **Wazuh Dashboard** and search:

```
rule.groups: virustotal
```

or:

```
rule.id: 87103
```

You should see:

```
VirusTotal: Alert - No records in VirusTotal database
```

> 📸 **Screenshot placeholder:** Wazuh Dashboard search results showing the VirusTotal alert entry.
> ```md
> ![Dashboard Verification](screenshots/dashboard-verification.png)
> ```

---

## 🔄 Complete Detection Flow

```
File Created/Modified
          |
          ↓
Windows Wazuh Agent
          |
          ↓
Syscheck detects change
          |
          ↓
Generate SHA256 Hash
          |
          ↓
Send Hash to VirusTotal API
          |
          ↓
Receive Reputation Result
          |
          ↓
Create Wazuh Alert
          |
          ↓
SOC Analyst Investigation
```

---

## ✅ Final Implementation Status

| Feature | Status |
|---|---|
| Wazuh Manager | ✅ Completed |
| Windows Agent Connection | ✅ Completed |
| File Integrity Monitoring | ✅ Completed |
| VirusTotal API Integration | ✅ Completed |
| Hash Extraction | ✅ Completed |
| Threat Intelligence Lookup | ✅ Completed |
| Alert Generation | ✅ Completed |
| Dashboard Visualization | ✅ Completed |

---

## 🛡️ Security Benefits

This integration provides:

- ✅ Automated malware reputation checking
- ✅ Faster incident detection
- ✅ Threat intelligence enrichment
- ✅ SOC investigation support
- ✅ Endpoint visibility
- ✅ Hash-based malware identification

---

## 🚀 Future Improvements

Possible next integrations to extend this lab into a full SOC platform:

- [ ] **MISP** Threat Intelligence Platform integration
- [ ] **Shuffle SOAR** automation
- [ ] Slack / Email alerting
- [ ] Malware sandbox integration (e.g., Cuckoo)
- [ ] Automated incident response playbooks

---

## 📌 Repository Structure

```
Wazuh-VirusTotal-Integration/
│
├── README.md
│
├── screenshots/
│     ├── lab-overview.png
│     ├── architecture-diagram.png
│     ├── step1-integrations-folder.png
│     ├── step2-virustotal-config.png
│     ├── step3-config-validation.png
│     ├── step4-manager-status.png
│     ├── step5-windows-fim-config.png
│     ├── step6-agent-service.png
│     ├── step7-test-file.png
│     ├── step8-fim-alert-log.png
│     ├── step9-virustotal-alert.png
│     └── dashboard-verification.png
│
├── configs/
│     ├── manager-ossec.conf
│     └── windows-agent-ossec.conf
│
└── documentation/
      └── setup-guide.md
```

---

## ⚠️ Important Security Note

**Never upload your real VirusTotal API key to GitHub.**

Before publishing, replace:

```xml
<api_key>YOUR_API_KEY</api_key>
```

with:

```xml
<api_key>REPLACE_WITH_YOUR_KEY</api_key>
```

Consider also adding a `.gitignore` entry for any config file containing real credentials, and use environment variables or secret managers for production deployments.

---

## 📄 License

This project is released under the [MIT License](LICENSE) — feel free to use and adapt it for your own SOC lab.

---

⭐ If this project helped you, consider giving the repository a star!
