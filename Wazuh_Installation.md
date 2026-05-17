# Wazuh Installation Lab Manual on Ubuntu Server

# 1. Objective

The objective of this lab is to install and configure the Wazuh SIEM platform on Ubuntu Server 22.04 LTS using a virtualized environment. This lab demonstrates how a Security Information and Event Management (SIEM) solution can be deployed for log monitoring, intrusion detection, endpoint visibility, and security analytics.

After completing this lab, the student will be able to:

* Install Ubuntu Server in VirtualBox
* Configure SSH connectivity
* Update and prepare the Linux environment
* Install Wazuh SIEM components
* Access the Wazuh web dashboard
* Verify Wazuh services and system status
* Understand the basic architecture of a SIEM platform

---

# 2. Introduction to Wazuh

Wazuh is an open-source Security Information and Event Management (SIEM) platform used for:

* Threat detection
* Log analysis
* Intrusion detection
* File integrity monitoring
* Vulnerability detection
* Endpoint security monitoring

Wazuh helps organizations monitor systems and detect suspicious activities in real time.

Main Components of Wazuh:

| Component       | Purpose                                        |
| --------------- | ---------------------------------------------- |
| Wazuh Manager   | Collects and analyzes logs                     |
| Wazuh Indexer   | Stores and indexes security data               |
| Wazuh Dashboard | Web interface for monitoring and visualization |
| Wazuh Agent     | Installed on monitored endpoints               |

---

# 3. Lab Requirements

## Hardware Requirements

| Component | Recommended    |
| --------- | -------------- |
| RAM       | Minimum 4 GB   |
| CPU       | 2 Virtual CPUs |
| Storage   | 50 GB          |
| Internet  | Required       |

## Software Requirements

| Software      | Version         |
| ------------- | --------------- |
| VirtualBox    | Latest          |
| Ubuntu Server | 22.04.5 LTS     |
| Wazuh         | 4.7             |
| SSH Client    | Windows OpenSSH |

---

# 4. Network Configuration

The Ubuntu Server virtual machine was configured inside VirtualBox.

## IP Address Information

The system automatically received the following IP address:

```bash
10.0.2.15
```

This IP address is used to access the Wazuh dashboard from the host machine.

---

# 5. SSH Connection to Ubuntu Server

The Ubuntu server was accessed remotely using SSH from Windows PowerShell.

## Command Used

```powershell
ssh rubaya@localhost
```

## Explanation

| Part      | Description           |
| --------- | --------------------- |
| ssh       | Secure Shell command  |
| rubaya    | Ubuntu username       |
| localhost | Local machine address |

## Successful Login Output

```bash
Welcome to Ubuntu 22.04.5 LTS
```

This indicates that SSH connectivity was successfully established.

---

# 6. Updating the Ubuntu System

Before installing Wazuh, the operating system must be updated.

## Command

```bash
sudo apt update && sudo apt upgrade -y
```
<img width="864" height="169" alt="image" src="https://github.com/user-attachments/assets/ab9b54a4-b3d2-44bb-a8c4-cb8dd98045f1" />


## Explanation

| Command     | Purpose                                        |
| ----------- | ---------------------------------------------- |
| sudo        | Execute command with administrative privileges |
| apt update  | Updates package repository information         |
| apt upgrade | Upgrades installed packages                    |
| -y          | Automatically confirms installation            |

## Expected Result

The system downloads and installs the latest updates.

---

# 7. Installing Curl Utility

Curl is required to download the Wazuh installation script.

## Command

```bash
sudo apt install curl -y
```
<img width="954" height="165" alt="image" src="https://github.com/user-attachments/assets/f1c686dc-bad9-4ecf-88fe-eab47cbd110f" />


## Explanation

| Command     | Purpose                    |
| ----------- | -------------------------- |
| apt install | Installs software packages |
| curl        | Data transfer utility      |
| -y          | Automatic confirmation     |

## Expected Result

Curl is installed successfully.

---

# 8. Downloading the Wazuh Installation Script

The Wazuh installation script was downloaded from the official Wazuh repository.

## Command

```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
```
<img width="998" height="32" alt="image" src="https://github.com/user-attachments/assets/6f05a74f-1dec-4753-ab51-1df856ae37c0" />

<img width="1042" height="209" alt="image" src="https://github.com/user-attachments/assets/276264a9-cf04-4dfd-bf5f-ffd502e68cc5" />


## Explanation

| Option | Purpose                               |
| ------ | ------------------------------------- |
| -s     | Silent mode                           |
| -O     | Saves the file with original filename |

## Expected Result

The file:

```bash
wazuh-install.sh
```

is downloaded successfully.

---

# 9. Making the Script Executable

The installation script must be given executable permission.

## Command

```bash
chmod +x wazuh-install.sh
```

## Explanation

| Command | Purpose                    |
| ------- | -------------------------- |
| chmod   | Changes file permissions   |
| +x      | Adds executable permission |

## Expected Result

The script becomes executable.

---

# 10. Installing Wazuh SIEM

The all-in-one installation method was used to install:

* Wazuh Manager
* Wazuh Dashboard
* Wazuh Indexer

## Command

```bash
sudo bash ./wazuh-install.sh -a
```

## Explanation

| Part             | Description                  |
| ---------------- | ---------------------------- |
| sudo             | Administrative privileges    |
| bash             | Executes shell script        |
| ./               | Current directory            |
| wazuh-install.sh | Installation script          |
| -a               | All-in-one installation mode |

## Installation Process

During installation:

* Required packages are downloaded
* Elasticsearch-based indexer is configured
* Wazuh manager is installed
* Dashboard service is configured
* SSL certificates are generated

## Expected Duration

Approximately:

```text
15–40 minutes
```

depending on internet speed and VM resources.

---

# 11. Generated Credentials

At the end of installation, Wazuh automatically generates login credentials.

## Example

```text
User: admin
Password: ********
```

These credentials are required to access the Wazuh Dashboard.

---

# 12. Verifying Wazuh Services

After installation, service status was verified.

## Checking Wazuh Manager

```bash
sudo systemctl status wazuh-manager
```

## Checking Wazuh Dashboard

```bash
sudo systemctl status wazuh-dashboard
```

## Checking Wazuh Indexer

```bash
sudo systemctl status wazuh-indexer
```

## Explanation

| Command   | Purpose                  |
| --------- | ------------------------ |
| systemctl | Controls system services |
| status    | Shows service status     |

## Expected Result

The services should display:

```text
active (running)
```

---

# 13. Accessing the Wazuh Dashboard

The Wazuh web dashboard can be accessed from the browser.

## URL

```text
https://10.0.2.15
```

## Login Credentials

| Username | Password                      |
| -------- | ----------------------------- |
| admin    | Generated during installation |

## Important Note

A browser warning may appear because Wazuh uses a self-signed SSL certificate.

Select:

```text
Advanced → Proceed
```

---

# 14. Dashboard Features Observed

After successful login, the following features were observed:

* Security Events Monitoring
* Agent Management
* Threat Detection
* File Integrity Monitoring
* Vulnerability Detection
* Log Analysis Dashboard
* Security Alerts Visualization

---

# 15. Basic Linux Commands Used During the Lab

| Command     | Purpose                           |
| ----------- | --------------------------------- |
| pwd         | Shows current directory           |
| ls          | Lists files                       |
| cd          | Changes directory                 |
| sudo        | Executes command as administrator |
| apt update  | Updates repositories              |
| apt upgrade | Upgrades packages                 |
| chmod       | Changes file permissions          |
| systemctl   | Manages services                  |
| ssh         | Remote secure connection          |

---

# 16. Common Errors and Solutions

## Error 1: SSH Connection Refused

### Error

```bash
ssh: connect to host localhost port 22: Connection refused
```

### Cause

SSH service was not running.

### Solution

```bash
sudo systemctl start ssh
```

---

## Error 2: PHP Command Not Recognized

### Error

```powershell
php : The term 'php' is not recognized
```

### Cause

PHP was not installed or not added to PATH.

### Solution

Install PHP or XAMPP and configure PATH variables.

---

## Error 3: Browser Cannot Access Wazuh Dashboard

### Cause

VirtualBox network configuration issue.

### Solution

* Configure Bridged Adapter or NAT Network
* Verify Ubuntu IP address
* Ensure Wazuh dashboard service is running

---

# 17. Security Benefits of Wazuh

Wazuh provides several cybersecurity advantages:

* Centralized log management
* Real-time threat monitoring
* Intrusion detection
* Malware detection support
* Compliance monitoring
* Endpoint visibility
* Security event correlation

It is widely used in SOC environments for defensive cybersecurity operations.

---

# 18. Conclusion

In this lab, Wazuh SIEM was successfully installed and configured on Ubuntu Server 22.04 LTS using VirtualBox. SSH connectivity was established from the Windows host machine, and the Wazuh dashboard was accessed through a web browser.

The lab demonstrated practical implementation of:

* Linux server administration
* SIEM deployment
* Security monitoring
* SOC environment setup
* Service management
* Remote administration using SSH

This experiment provided hands-on experience with enterprise-level security monitoring tools used in real-world cybersecurity operations.

---

# 19. References

## Official Documentation

* Wazuh Documentation
  [https://documentation.wazuh.com/](https://documentation.wazuh.com/)

* Ubuntu Documentation
  [https://help.ubuntu.com/](https://help.ubuntu.com/)

* VirtualBox Documentation
  [https://www.virtualbox.org/wiki/Documentation](https://www.virtualbox.org/wiki/Documentation)

---

# 20. Appendix

## Complete Installation Commands

```bash
sudo apt update && sudo apt upgrade -y

sudo apt install curl -y

curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh

chmod +x wazuh-install.sh

sudo bash ./wazuh-install.sh -a

sudo systemctl status wazuh-manager

sudo systemctl status wazuh-dashboard

sudo systemctl status wazuh-indexer
```

---

# End of Lab Manual
