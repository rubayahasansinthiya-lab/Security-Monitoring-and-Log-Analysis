# Packet Analysis with Wireshark

> A hands-on network forensics lab focused on packet analysis, IOC extraction, suspicious traffic detection, and incident response using Wireshark.

---

## Overview

This project demonstrates how to investigate suspicious network traffic using Wireshark.

The objective is to analyze a packet capture (PCAP) file, identify malicious activity, extract indicators of compromise (IOCs), and document the findings in a professional incident report.

This lab simulates the workflow of a Security Operations Center (SOC) analyst during a real-world security investigation.

---

## Learning Objectives

By completing this lab, I was able to:

* Navigate the Wireshark interface efficiently
* Apply display filters to isolate traffic
* Analyze DNS, HTTP, HTTPS, TCP, and UDP traffic
* Reconstruct conversations using TCP streams
* Extract files from packet captures
* Detect suspicious network behavior
* Identify indicators of compromise (IOCs)
* Create a professional incident analysis report

---

## Tools Used

* Wireshark
* tshark
* VirusTotal
* URLhaus
* AbuseIPDB

---

## Project Structure

```text
Packet-Analysis-with-Wireshark/
│
├── README.md
├── Packet-Analysis-Report.md
├── IOCs.txt
└── screenshots/
```

---

## Investigation Workflow

```text
Open PCAP
    ↓
Identify User IP
    ↓
Analyze DNS Traffic
    ↓
Inspect HTTP Requests
    ↓
Follow TCP Streams
    ↓
Export Objects
    ↓
Extract IOCs
    ↓
Create Incident Report
```

---

## Wireshark Display Filters

### 1. Show HTTP Traffic

#### Filter

```text
http
```

#### Purpose

Displays all HTTP traffic.

#### Use Case

Identify websites accessed over HTTP.

#### Screenshot

![HTTP Traffic](screenshots/02-http-filter.png)

---

### 2. Show DNS Traffic

#### Filter

```text
dns
```

#### Purpose

Displays all DNS packets.

#### Use Case

Identify queried domains.

#### Screenshot

![DNS Traffic](screenshots/03-dns-query.png)

---

### 3. Show DNS Queries Only

#### Filter

```text
dns.flags.response == 0
```

#### Breakdown

* `dns` → Domain Name System protocol
* `flags.response` → Identifies query or response packets
* `0` → Query packets only

#### Use Case

Identify domains requested by the user.

#### Screenshot

![DNS Queries](screenshots/03-dns-query.png)

---

### 4. Filter Traffic by IP Address

#### Filter

```text
ip.addr == 192.168.1.100
```

#### Breakdown

* `ip.addr` → Source or destination IP address
* `==` → Equals

#### Use Case

Investigate all traffic related to a specific host.

---

### 5. Show HTTP Requests Only

#### Filter

```text
http.request
```

#### Use Case

Identify user activity and requested resources.

#### Screenshot

![HTTP Requests](screenshots/02-http-filter.png)

---

### 6. Show TCP SYN Packets

#### Filter

```text
tcp.flags.syn == 1
```

#### Use Case

Identify newly established TCP connections.

---

### 7. Filter HTTP and HTTPS Traffic

#### Filter

```text
tcp.port == 80 || tcp.port == 443
```

#### Breakdown

* `80` → HTTP
* `443` → HTTPS
* `||` → OR operator

#### Use Case

Analyze web traffic.

---

### 8. Show HTTP POST Requests

#### Filter

```text
http.request.method == "POST"
```

#### Use Case

Detect data uploads and possible exfiltration.

---

### 9. Filter Large Packets

#### Filter

```text
ip.len > 1000
```

#### Use Case

Identify potential file transfers or data exfiltration.

---

## TCP Stream Analysis

### Navigation

```text
Right Click → Follow → TCP Stream
```

### Purpose

Reconstruct complete conversations between hosts.

### Use Cases

* Analyze malware communication
* Extract credentials
* Review transferred data

### Screenshot

![TCP Stream](screenshots/04-follow-tcp-stream.png)

---

## HTTP Object Extraction

### Navigation

```text
File → Export Objects → HTTP
```

### Purpose

Extract files transferred over HTTP.

### Common File Types

* .exe
* .dll
* .zip
* .docm
* .ps1

### Screenshot

![HTTP Export](screenshots/05-http-export.png)

---

## Protocol Statistics

### Navigation

```text
Statistics → Protocol Hierarchy
```

### Purpose

Understand traffic composition.

### Screenshot

![Protocol Hierarchy](screenshots/07-protocol-hierarchy.png)

---

## Conversation Analysis

### Navigation

```text
Statistics → Conversations
```

### Purpose

Identify:

* Top talkers
* Large transfers
* Suspicious communications

### Screenshot

![Conversations](screenshots/06-conversations.png)

---

## Indicators of Compromise (IOCs)

The extracted IOCs are documented separately in the `IOCs.txt` file.

IOC categories include:

* Domains
* IP addresses
* URLs
* File hashes

---

## Skills Demonstrated

* Network Traffic Analysis
* Packet Inspection
* Network Forensics
* IOC Extraction
* Threat Hunting
* Incident Response
* Technical Documentation

---

## References

* https://www.wireshark.org/docs/
* https://www.malware-traffic-analysis.net/
* https://urlhaus.abuse.ch/
* https://www.abuseipdb.com/
* https://www.virustotal.com/

---

## Author

**Rubaya Hasan Sinthiya**

Cybersecurity Enthusiast | SOC Analyst Trainee

