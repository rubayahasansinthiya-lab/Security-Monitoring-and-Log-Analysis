# Lab: Log Analysis with CLI Tools

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
<br>
<img width="507" height="49" alt="image" src="https://github.com/user-attachments/assets/75434fd5-328b-4c54-9d77-f3beb57cd0bb" />
<br>

---

## Extract IP Addresses

```bash
awk '{print $1}' access.log
```
<br>
<img width="672" height="323" alt="image" src="https://github.com/user-attachments/assets/05cf5b79-08af-4497-9c2a-0a1570698096" />
<br>
---

## Find Unique IPs

```bash
awk '{print $1}' access.log | sort -u
```
<br>
<img width="691" height="127" alt="image" src="https://github.com/user-attachments/assets/0a688ee7-e1ba-49aa-8294-1e8ff60ffc6a" />
<br>

---

## Count Unique IPs

```bash
awk '{print $1}' access.log | sort -u | wc -l
```
<br>
<img width="740" height="45" alt="image" src="https://github.com/user-attachments/assets/dd1bb3f6-c12a-48ca-b226-f168bbf3e9e9" />
<br>
---

## Top IP Addresses

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head
```
<br>
<img width="901" height="129" alt="image" src="https://github.com/user-attachments/assets/4f90f5a4-4936-474c-aecf-9184e35aa059" />
<br>
---

## Count 404 Errors

```bash
grep " 404 " access.log | wc -l
```
<br>
<img width="565" height="47" alt="image" src="https://github.com/user-attachments/assets/3dce2f8e-f218-435d-83f4-173349dca804" />
<br>
OR

```bash
awk '$9 == 404' access.log | wc -l
```
<br>
<img width="597" height="45" alt="image" src="https://github.com/user-attachments/assets/24b82e3a-cdb0-4fd7-8bba-c33c86de0ff9" />
<br>
---

## HTTP Status Code Distribution

```bash
awk '{print $9}' access.log | sort | uniq -c | sort -rn
```
<br>
<img width="817" height="67" alt="image" src="https://github.com/user-attachments/assets/2ca8e3ba-408c-4a36-b22c-486a73a42703" />
<br>
---

## Top 404 URLs

```bash
awk '$9 == 404 {print $7}' access.log | sort | uniq -c | sort -rn | head
```
<br>
<img width="960" height="146" alt="image" src="https://github.com/user-attachments/assets/a87f5672-1a79-45ad-838c-4da8e5bbd1ca" />
<br>
---

# Part 3: Detect Suspicious Activity

## Detect Nikto Scanning

```bash
grep -i "nikto" access.log
```
<br>

Count results:

```bash
grep -i "nikto" access.log | wc -l
```
<br>
<img width="655" height="41" alt="image" src="https://github.com/user-attachments/assets/d6269184-a5b5-4325-8130-8a8477e630ef" />
<br>
---

## Detect SQL Injection Attempts

```bash
grep -iE "(union|select|insert|drop|--)" access.log
```
<br>
<img width="821" height="27" alt="image" src="https://github.com/user-attachments/assets/6a82a50f-4079-41b3-ab41-2d89acb9901a" />
<br>
---

## Detect XSS Attempts

```bash
grep -iE "(<script|javascript:|onerror=)" access.log
```
<br>
<img width="818" height="32" alt="image" src="https://github.com/user-attachments/assets/61b3c5de-0d7c-42e9-86c3-76ddf86625c9" />
<br>
---

## Detect Directory Traversal

```bash
grep -E "(\.\./|\.\.%2[Ff])" access.log
```
<br>
<img width="719" height="27" alt="image" src="https://github.com/user-attachments/assets/358bfae8-c01a-4daf-a5a7-80529e1b35c2" />
<br>
---

# Part 4: Traffic Analysis

## Analyze Traffic by Hour

```bash
awk '{print $4}' access.log | cut -d: -f2 | sort | uniq -c | sort -rn
```
<br>
<img width="930" height="44" alt="image" src="https://github.com/user-attachments/assets/c828afa6-3e32-4983-8051-846851daea91" />
<br>
---

## Analyze HTTP Methods

```bash
awk '{print $6}' access.log | tr -d '"' | sort | uniq -c | sort -rn
```
<br>
<img width="925" height="66" alt="image" src="https://github.com/user-attachments/assets/b5b7f8e2-c030-4d36-8ae1-2544926a2f9c" />
<br>
---

## Extract POST Requests

```bash
awk '$6 == "\"POST" {print $1}' access.log | sort -u
```
<br>
<img width="812" height="48" alt="image" src="https://github.com/user-attachments/assets/9533f53c-542f-41eb-a9ab-162b19729cdf" />
<br>
---

## Calculate Bandwidth Usage

```bash
awk '{sum += $10} END {print sum/1024/1024 " MB"}' access.log
```
<br>
<img width="861" height="45" alt="image" src="https://github.com/user-attachments/assets/0ac38f1d-a5c0-4804-bba2-669c67055414" />
<br>
---

# Part 5: File Operations

## Create Unique IP List

```bash
awk '{print $1}' access.log | sort -u > unique_ips.txt
```
<br>
<img width="832" height="30" alt="image" src="https://github.com/user-attachments/assets/4940c244-bc1d-49f1-8638-c0c2a22840c6" />
<br>
---

## Extract URLs

```bash
awk '{print $7}' access.log | sort -u > urls.txt
```
<br>
<img width="801" height="26" alt="image" src="https://github.com/user-attachments/assets/9cb8b95f-dc4c-43b8-931c-7918471cc738" />
<br>
---

## Anonymize IP Addresses

```bash
sed 's/\.[0-9]\{1,3\}$/\.XXX/' access.log
```
<br>
<img width="953" height="744" alt="image" src="https://github.com/user-attachments/assets/aa2d1339-b768-4469-8eb0-39778bcb2a3e" />
<br>
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
