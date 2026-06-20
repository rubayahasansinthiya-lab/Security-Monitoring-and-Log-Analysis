# Packet Analysis Report

## Executive Summary

This report documents the investigation of a packet capture (PCAP) file using Wireshark.

The objective of the investigation was to identify suspicious network activity, extract indicators of compromise (IOCs), and document the findings.

---

## Scope

* Analyze captured network traffic
* Identify suspicious domains and IP addresses
* Detect Command and Control (C2) communication
* Investigate file downloads and uploads
* Extract indicators of compromise (IOCs)

---

## Lab Environment

| Component        | Details               |
| ---------------- | --------------------- |
| Analysis Tool    | Wireshark             |
| Operating System | Windows 11            |
| Capture Format   | PCAP                  |
| Analyst          | Rubaya Hasan Sinthiya |

---

## Network Information

| Item        | Value |
| ----------- | ----- |
| Internal IP | TBD   |
| Gateway     | TBD   |
| DNS Server  | TBD   |

---

## Timeline of Events

| Time | Event         | Details                     |
| ---- | ------------- | --------------------------- |
| TBD  | DNS Query     | User queried a domain       |
| TBD  | HTTP Request  | Connection established      |
| TBD  | File Download | Suspicious file transferred |

---

## Malicious Activity Detected

### Suspicious Domains

* TBD

### Suspicious IP Addresses

* TBD

### File Downloads

* TBD

### Command and Control (C2)

* TBD

### Data Exfiltration

* TBD

---

## Indicators of Compromise

Refer to the `IOCs.txt` file.

---

## MITRE ATT&CK Mapping

| Tactic              | Technique                     | ID    |
| ------------------- | ----------------------------- | ----- |
| Command and Control | Application Layer Protocol    | T1071 |
| Exfiltration        | Exfiltration Over Web Service | T1567 |

---

## Recommendations

* Block malicious domains and IP addresses.
* Monitor outbound HTTP and HTTPS traffic.
* Review endpoint logs for suspicious activity.
* Update detection rules with extracted IOCs.

---

## Conclusion

The investigation identified suspicious network activity requiring additional analysis.

Further monitoring and IOC validation are recommended.
