# 🛡️ Wazuh SIEM + VirusTotal Threat Intelligence Integration

[![Wazuh](https://img.shields.io/badge/Wazuh-4.x-blue)](https://wazuh.com/)
[![VirusTotal](https://img.shields.io/badge/VirusTotal-API-green)](https://www.virustotal.com/)
[![Status](https://img.shields.io/badge/Status-Working-brightgreen)]()

## 📌 Project Overview

This project demonstrates a full, working integration between **Wazuh SIEM** and the **VirusTotal Threat Intelligence Platform** to perform automated malware/hash reputation checking on a Windows endpoint.

The system monitors file changes using **Wazuh File Integrity Monitoring (FIM / Syscheck)** and automatically sends the file's cryptographic hash to VirusTotal for analysis. If the hash is known and malicious, an alert is raised in the Wazuh dashboard.

```
Windows Endpoint
      │
      ▼
Wazuh Agent (FIM / Syscheck)
      │
      ▼
Wazuh Manager
      │
      ▼
VirusTotal API
      │
      ▼
Threat Intelligence Result
      │
      ▼
Wazuh Dashboard Alert
```

---

## 🎯 Objectives

- Deploy a working Wazuh Manager on Ubuntu Server
- Connect a Windows endpoint via Wazuh Agent
- Enable File Integrity Monitoring (FIM) on a target directory
- Integrate the VirusTotal API with Wazuh
- Automatically analyze file hashes on modification
- Generate and verify security alerts
- Visualize threat intelligence results on the Wazuh Dashboard

---

## 🏗️ Lab Architecture

```
                     Internet
                        │
                        ▼
                 VirusTotal API
                        │
                        ▼
   ┌───────────────────────────────────────┐
   │             Ubuntu Server              │
   │                                       │
   │   • Wazuh Manager                     │
   │   • Wazuh Analysis Engine             │
   │   • VirusTotal Integration Module     │
   │                                       │
   │   IP: 10.29.94.131                    │
   └───────────────────────────────────────┘
                        │
                     TCP 1514
                        │
   ┌───────────────────────────────────────┐
   │            Windows Endpoint            │
   │                                       │
   │   • Wazuh Agent                       │
   │   • File Integrity Monitoring (FIM)   │
   │                                       │
   │   IP: 10.29.94.14                     │
   └───────────────────────────────────────┘
```

---

## 🛠️ Technologies Used

| Component | Purpose |
|---|---|
| Wazuh SIEM | Security monitoring and alerting |
| Wazuh Agent | Endpoint monitoring |
| Ubuntu Server | Wazuh Manager host |
| Windows 11 | Endpoint machine |
| VirusTotal API | Threat intelligence lookup |
| Syscheck / FIM | File integrity monitoring |

---

## ⚙️ Step 1 — Verify Wazuh Installation

Check core Wazuh directories on the manager:

```bash
sudo ls /var/ossec/
```

Check available integration scripts:

```bash
sudo ls /var/ossec/integrations/
```

Expected output should include:

```
virustotal
virustotal.py
```

---

## ⚙️ Step 2 — Configure the VirusTotal Integration on the Manager

Edit the Wazuh manager configuration file:

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

> ⚠️ **Never commit your real API key.** See the [Security Note](#️-important-security-note) at the bottom of this document.

---

## ⚙️ Step 3 — Validate the Configuration

```bash
sudo /var/ossec/bin/wazuh-analysisd -t
```

Expected output:

```
Configuration OK
```

---

## ⚙️ Step 4 — Restart the Wazuh Manager

```bash
sudo systemctl restart wazuh-manager
sudo systemctl status wazuh-manager
```

Expected status:

```
active (running)
```

---

## ⚙️ Step 5 — Confirm the Windows Agent Is Connected

On the Wazuh Manager:

```bash
sudo /var/ossec/bin/agent_control -l
```

The target Windows agent must show as:

```
Active
```

If it shows `Disconnected`, reconnect/re-enroll the agent before continuing.

📸 **[ADD SCREENSHOT HERE]** → `screenshots/agent-active-status.png` (terminal output showing the agent as `Active`)

---

## ⚙️ Step 6 — Configure FIM (Syscheck) on the Windows Agent

Open the agent configuration file on the Windows machine:

```
C:\Program Files (x86)\ossec-agent\ossec.conf
```

Locate the `<syscheck>` block and add a **realtime** monitored directory:

```xml
<syscheck>

  <disabled>no</disabled>

  <frequency>43200</frequency>

  <directories recursion_level="0" restrict="regedit.exe$|system.ini$|win.ini$">%WINDIR%</directories>

  <directories recursion_level="0" restrict="at.exe$|attrib.exe$|cacls.exe$|cmd.exe$|eventcreate.exe$|ftp.exe$|lsass.exe$|net.exe$|net1.exe$|netsh.exe$|reg.exe$|regedt32.exe|regsvr32.exe|runas.exe|sc.exe|schtasks.exe|sethc.exe|subst.exe$">%WINDIR%\SysNative</directories>

  <directories recursion_level="0">%WINDIR%\SysNative\drivers\etc</directories>

  <directories realtime="yes">%PROGRAMDATA%\Microsoft\Windows\Start Menu\Programs\Startup</directories>

  <!-- Custom monitored path for VirusTotal integration testing -->
  <directories realtime="yes">C:\Users\Public</directories>

  <ignore>%PROGRAMDATA%\Microsoft\Windows\Start Menu\Programs\Startup\desktop.ini</ignore>

  <ignore type="sregex">.log$|.htm$|.jpg$|.png$|.chm$|.pnf$|.evtx$</ignore>

</syscheck>
```

Save the file.

---

## ⚙️ Step 7 — Restart the Windows Agent

Open **PowerShell as Administrator**:

```powershell
Restart-Service WazuhSvc
Get-Service WazuhSvc
```

Expected status:

```
Running
```

📸 **[ADD SCREENSHOT HERE]** → `screenshots/windows-agent-running.png` (PowerShell output of `Get-Service WazuhSvc`)

---

## 🧪 Step 8 — Test File Integrity Monitoring

Create a test file:

```powershell
New-Item C:\Users\Public\vt-test.txt -ItemType File
Set-Content C:\Users\Public\vt-test.txt "VirusTotal Test"
```

Modify it again to trigger a second FIM event:

```powershell
Set-Content C:\Users\Public\vt-test.txt "Modified Test"
```

---

## 🔍 Step 9 — Verify the FIM Alert on the Manager

On Ubuntu, tail the alerts log:

```bash
sudo tail -f /var/ossec/logs/alerts/alerts.json
```

You should see an entry similar to:

```json
"rule": {
  "level": 7,
  "description": "Integrity checksum changed.",
  "groups": ["ossec", "syscheck", "syscheck_entry_modified", "syscheck_file"]
}
```

This confirms:

- ✅ File modification was detected
- ✅ MD5 / SHA1 / SHA256 hashes were generated
- ✅ Alert was sent from Agent → Manager

📸 **[ADD SCREENSHOT HERE]** → `screenshots/fim-alert.png` (terminal or dashboard view of the `alerts.json` FIM entry)

---

## 🦠 Step 10 — Verify the VirusTotal Result

Once FIM detects the change, Wazuh automatically submits the file hash to VirusTotal. Look for an entry like:

```json
"rule": {
  "description": "VirusTotal: Alert - No records in VirusTotal database",
  "groups": ["virustotal"]
},
"data": {
  "virustotal": {
    "found": "0",
    "malicious": "0"
  }
}
```

**Meaning:**

| Field | Value | Meaning |
|---|---|---|
| `found` | 0 | Hash does not exist in VirusTotal's database |
| `malicious` | 0 | No malicious detections reported |

This proves the full pipeline — FIM → hash extraction → VirusTotal API → alert — is working end to end.

📸 **[ADD SCREENSHOT HERE]** → `screenshots/virustotal-terminal-result.png` (terminal view of the VirusTotal JSON alert)

---

## 📊 Step 11 — Verify on the Wazuh Dashboard

Open the Wazuh Dashboard → **Security Events / Threat Hunting** and search for:

```
rule.groups: virustotal
```

or by rule ID:

```
rule.id: 87103
```

You should see the alert:

```
VirusTotal: Alert - No records in VirusTotal database
```

📸 **[ADD SCREENSHOT HERE]** → `screenshots/wazuh-dashboard.png` (Wazuh Dashboard search results showing the VirusTotal alert)

---

## 🧫 Step 12 (Optional) — Test With a Known Detection Using EICAR

The normal `.txt` test file only validates the pipeline — it will never show as malicious because VirusTotal has no record of that hash. To simulate an actual detection safely, use the industry-standard **EICAR test file** (harmless, recognized by all antivirus engines as a test signature):

```powershell
Set-Content C:\Users\Public\eicar-test.com 'X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*'
```

Expected result this time:

```json
"virustotal": {
  "found": "1",
  "malicious": "1"
}
```

📸 **[ADD SCREENSHOT HERE]** → `screenshots/eicar-detection-result.png` (alert showing `found: 1, malicious: 1` for the EICAR test)

---

## 🔄 Complete Detection Flow

```
File Created / Modified
          │
          ▼
Windows Wazuh Agent
          │
          ▼
Syscheck detects the change
          │
          ▼
Generate SHA256 hash
          │
          ▼
Send hash to VirusTotal API
          │
          ▼
Receive reputation result
          │
          ▼
Create Wazuh alert
          │
          ▼
SOC Analyst investigation
```

---

## ✅ Final Implementation Status

| Feature | Status |
|---|---|
| Wazuh Manager | ✅ Completed |
| Windows Agent Connection | ✅ Completed |
| File Integrity Monitoring (FIM) | ✅ Completed |
| VirusTotal API Integration | ✅ Completed |
| Hash Extraction | ✅ Completed |
| Threat Intelligence Lookup | ✅ Completed |
| Alert Generation | ✅ Completed |
| Dashboard Visualization | ✅ Completed |

---

## 🛡️ Security Benefits

- ✅ Automated malware reputation checking
- ✅ Faster incident detection
- ✅ Threat intelligence enrichment
- ✅ SOC investigation support
- ✅ Endpoint visibility
- ✅ Hash-based malware identification

---

## 🚀 Future Improvements

- [ ] MISP Threat Intelligence Platform integration
- [ ] Shuffle SOAR automation
- [ ] Slack / Email alerting on detections
- [ ] Malware sandbox integration (e.g., Cuckoo)
- [ ] Automated incident response playbooks

---

## 📌 Recommended Repository Structure

```
Wazuh-VirusTotal-Integration/
│
├── README.md
│
├── screenshots/
│     ├── wazuh-dashboard.png
│     ├── virustotal-alert.png
│     └── fim-alert.png
│
├── configs/
│     ├── manager-ossec.conf
│     └── windows-agent-ossec.conf
│
└── documentation/
      └── setup-guide.md
```

---

## 🔧 Troubleshooting Reference

| Symptom | Likely Cause | Fix |
|---|---|---|
| Agent shows `Disconnected` | Agent service stopped / firewall blocking 1514 | Restart `WazuhSvc`, check network/firewall |
| No syscheck alert | Directory not added to `<syscheck>` or agent not restarted | Re-check config, restart `WazuhSvc` |
| No VirusTotal alert | Integration block missing/misconfigured, or manager not restarted | Re-check `<integration>` block, restart `wazuh-manager` |
| `found: 0` always | Normal/custom test files have no VirusTotal history | Use the EICAR test file for a real detection |
| Config test fails | XML syntax error in `ossec.conf` | Run `wazuh-analysisd -t` and fix reported line |

---

## ⚠️ Important Security Note

**Never upload your real VirusTotal API key to GitHub.**

Before publishing this repository, replace:

```xml
<api_key>YOUR_REAL_API_KEY</api_key>
```

with:

```xml
<api_key>REPLACE_WITH_YOUR_KEY</api_key>
```

If a real key was ever committed or shared, **revoke and regenerate it** from your VirusTotal account immediately.

---

## 📄 License

This documentation is provided for educational and lab/training purposes as part of a personal SOC / Threat Intelligence learning project.
