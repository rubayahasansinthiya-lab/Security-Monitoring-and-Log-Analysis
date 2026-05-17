# Week 3 Lab: Log Analysis with CLI Tools

---

# Learning Outcomes

By the end of this lab, you will be able to:

- Use grep to search patterns in log files
- Use awk to extract and analyze log fields
- Use sed for text transformations
- Combine Linux CLI tools for log analysis
- Detect suspicious web server activities
- Create a simple SOC investigation report

---

# Objective

Learn how to use Linux command-line tools such as:

- grep
- awk
- sed
- cut
- sort
- uniq

to analyze Apache web server logs for security investigations.

---

# Scenario

You are a SOC analyst at SecureCorp. The security team detected suspicious traffic on a web server. Your task is to analyze Apache access logs to identify:

- Suspicious IP addresses
- Attack attempts
- Unusual traffic patterns
- Potential reconnaissance activities

---

# Prerequisites

- Ubuntu or Kali Linux
- Basic Linux command knowledge
- Apache log understanding

---

# Lab Duration

Approximately 2–3 hours

---

# Part 1: Apache Log Format

Apache Combined Log Format:

```text
%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"
```

Example:

```text
192.168.1.100 - - [10/Jan/2024:13:55:36 +0000] "GET /index.html HTTP/1.1" 200 2326 "-" "Mozilla/5.0"
```

---

# Part 2: Basic Log Analysis

## Count Total Requests

```bash
wc -l access.log
```

---

## Extract IP Addresses

```bash
awk '{print $1}' access.log
```

---

## Find Unique IPs

```bash
awk '{print $1}' access.log | sort -u
```

---

## Count Unique IPs

```bash
awk '{print $1}' access.log | sort -u | wc -l
```

---

## Top IP Addresses

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head
```

---

## Count 404 Errors

```bash
grep " 404 " access.log | wc -l
```

OR

```bash
awk '$9 == 404' access.log | wc -l
```

---

## HTTP Status Code Distribution

```bash
awk '{print $9}' access.log | sort | uniq -c | sort -rn
```

---

## Top 404 URLs

```bash
awk '$9 == 404 {print $7}' access.log | sort | uniq -c | sort -rn | head
```

---

# Part 3: Detect Suspicious Activity

## Detect Nikto Scanning

```bash
grep -i "nikto" access.log
```

Count results:

```bash
grep -i "nikto" access.log | wc -l
```

---

## Detect SQL Injection Attempts

```bash
grep -iE "(union|select|insert|drop|--)" access.log
```

---

## Detect XSS Attempts

```bash
grep -iE "(<script|javascript:|onerror=)" access.log
```

---

## Detect Directory Traversal

```bash
grep -E "(\.\./|\.\.%2[Ff])" access.log
```

---

# Part 4: Traffic Analysis

## Analyze Traffic by Hour

```bash
awk '{print $4}' access.log | cut -d: -f2 | sort | uniq -c | sort -rn
```

---

## Analyze HTTP Methods

```bash
awk '{print $6}' access.log | tr -d '"' | sort | uniq -c | sort -rn
```

---

## Extract POST Requests

```bash
awk '$6 == "\"POST" {print $1}' access.log | sort -u
```

---

## Calculate Bandwidth Usage

```bash
awk '{sum += $10} END {print sum/1024/1024 " MB"}' access.log
```

---

# Part 5: File Operations

## Create Unique IP List

```bash
awk '{print $1}' access.log | sort -u > unique_ips.txt
```

---

## Extract URLs

```bash
awk '{print $7}' access.log | sort -u > urls.txt
```

---

## Anonymize IP Addresses

```bash
sed 's/\.[0-9]\{1,3\}$/\.XXX/' access.log
```

---

# Part 6: Security Findings

## Normal Activities

- Browser requests
- CSS and JS loading
- Normal page access

---

## Suspicious Activities

- Nikto vulnerability scanning
- Multiple 404 requests
- Admin page probing
- Possible reconnaissance attempts

---

# Part 7: Report Template

Create:

```bash
nano Log-Analysis-Report.md
```

Example structure:

```markdown
# Apache Log Analysis Report

## Total Requests
wc -l access.log

## Unique IPs
awk '{print $1}' access.log | sort -u | wc -l

## Top IPs
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head

## 404 Analysis
awk '$9 == 404 {print $7}' access.log | sort | uniq -c | sort -rn | head

## Security Findings
- Nikto detected
- SQL injection attempts analyzed
- XSS attempts checked

## Conclusion
Summary of investigation
```

---

# Deliverables

Submit the following:

- access.log
- Log-Analysis-Report.md
- unique_ips.txt
- urls.txt

---

# Evaluation Criteria

- Correct command usage
- Quality of analysis
- Security understanding
- Professional documentation

---

# Conclusion

This lab demonstrated how SOC analysts use Linux CLI tools to investigate Apache web server logs and identify suspicious activities.

---

# End of Lab
