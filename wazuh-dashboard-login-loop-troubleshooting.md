# 🛡️ Wazuh Dashboard Login Loop & Authentication Fix — Complete Troubleshooting Guide

> A complete, tested reference for diagnosing and fixing the classic **"login → loading → login page again"** loop on Wazuh Dashboard.

---

## 📌 Problem Summary

**Symptom:**

Wazuh Dashboard opens fine at:

```
http://SERVER_IP:5601
```

But after entering username/password:

- ❌ Login does not complete
- ❌ No error message is shown
- ❌ Browser redirects back to the login page

```
Login → Loading → Login Page Again
```

---

## 🏗️ Wazuh Architecture Overview

Wazuh Dashboard login depends on three components working together:

```
Browser
   │
   ▼
Wazuh Dashboard   (port 5601)
   │
   ▼
Wazuh Indexer / OpenSearch   (port 9200)
   │
   ▼
Wazuh API   (port 55000)
```

When login fails, check **all three** layers:

1. Dashboard
2. Indexer
3. Wazuh API

---

## 🔍 Step-by-Step Diagnosis

### Step 1 — Find the Ubuntu Server IP

```bash
hostname -I
```

Example output:

```
172.30.31.202
```

Dashboard URL becomes:

```
http://172.30.31.202:5601
```

---

### Step 2 — Check Dashboard Port

```bash
sudo ss -tlnp | grep 5601
```

Expected output:

```
LISTEN 0 511 0.0.0.0:5601
```

✅ Means the dashboard service is running and the port is open.

---

### Step 3 — Check Network Connectivity (from Windows)

```powershell
Test-NetConnection SERVER_IP -Port 5601
```

Example:

```powershell
Test-NetConnection 172.30.31.202 -Port 5601
```

Expected result:

```
TcpTestSucceeded : True
```

✅ Confirms the client machine can reach the dashboard.

---

### Step 4 — Check Dashboard Configuration File

File location:

```
/etc/wazuh-dashboard/opensearch_dashboards.yml
```

Open it:

```bash
sudo nano /etc/wazuh-dashboard/opensearch_dashboards.yml
```

Key settings to verify:

```yaml
server.host: "0.0.0.0"
server.port: 5601
opensearch.hosts: https://127.0.0.1:9200
```

---

## 🐛 Problem 1 — Login Loop Caused by Cookie `secure` Setting

**Symptom:**

Even with the correct password, the browser keeps bouncing back:

```
Login → Login Page
```

**Root Cause:**

```yaml
opensearch_security.cookie.secure: true
```

while:

```yaml
server.ssl.enabled: false
```

The dashboard is running over **HTTP**, but the cookie is configured to require **HTTPS** — a direct conflict that silently breaks the session.

**Fix:**

```bash
sudo nano /etc/wazuh-dashboard/opensearch_dashboards.yml
```

| Before | After |
|---|---|
| `opensearch_security.cookie.secure: true` | `opensearch_security.cookie.secure: false` |

Restart the service:

```bash
sudo systemctl restart wazuh-dashboard
```

---

### Step 5 — Find the Default Wazuh Password

During installation, Wazuh generates a password file.

```bash
sudo tar -xf ~/wazuh-install-files.tar
sudo cat ~/wazuh-install-files/wazuh-passwords.txt
```

Example content:

```
indexer_username: admin
indexer_password: ************
```

**Dashboard login credentials:**

- Username: `admin`
- Password: *(value from the file above)*

> ⚠️ **Important:** Do **not** use `kibanaserver` — that is an internal service account, not meant for interactive login.

---

### Step 6 — Verify Indexer Authentication

```bash
curl -k -u admin:'PASSWORD' https://127.0.0.1:9200
```

Successful response:

```json
{
  "name": "node-1",
  "cluster_name": "wazuh-cluster"
}
```

✅ Confirms the password is correct and the Indexer is healthy.

---

### Step 7 — Check Dashboard Logs

```bash
sudo journalctl -u wazuh-dashboard -n 100 --no-pager
```

Look for errors such as:

```
GET /api/check-wazuh 401
```

This indicates the **Dashboard → Wazuh API** authentication is failing.

---

### Step 8 — Verify Wazuh API Credentials

Credentials are in the same file:

```
wazuh-passwords.txt
```

Example:

```
api_username: wazuh
api_password: ********
```

Test authentication directly:

```bash
curl -k -u wazuh:'API_PASSWORD' \
  -X POST \
  https://127.0.0.1:55000/security/user/authenticate?raw=true
```

Successful output (a JWT token):

```
eyJhbGciOi...
```

✅ Confirms the Wazuh API authentication is working.

---

## 🐛 Problem 2 — Wazuh Dashboard API Configuration Mismatch

Main config file:

```
/usr/share/wazuh-dashboard/data/wazuh/config/wazuh.yml
```

```bash
sudo nano /usr/share/wazuh-dashboard/data/wazuh/config/wazuh.yml
```

**Problematic configuration:**

```yaml
hosts:
  - default:
      url: https://localhost
      port: 55000
      username: wazuh-wui
      password: "xxxxx"
```

**Fix — if `wazuh-wui` fails authentication, switch to:**

```yaml
hosts:
  - default:
      url: https://localhost
      port: 55000
      username: wazuh
      password: "API_PASSWORD"
      run_as: false
```

Save the file (nano):

```
CTRL + O   →  ENTER   →   CTRL + X
```

Restart the dashboard:

```bash
sudo systemctl restart wazuh-dashboard
```

---

### Step 10 — Clear Browser Session

Stale cookies/IP bindings can also cause the loop.

1. Close the browser completely
2. Open an **Incognito/Private** window (Chrome: `CTRL + SHIFT + N`)
3. Navigate to `http://SERVER_IP:5601`
4. Log in with `admin` / `PASSWORD`

---

## ⚙️ Useful Wazuh Service Commands

**Check service status:**

```bash
sudo systemctl status wazuh-dashboard
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-manager
```

**Restart services:**

```bash
sudo systemctl restart wazuh-dashboard
sudo systemctl restart wazuh-indexer
sudo systemctl restart wazuh-manager
```

---

## 🧭 Complete Troubleshooting Flow (Quick Reference)

```
1. Check server IP           → hostname -I
2. Check dashboard port      → ss -tlnp | grep 5601
3. Check network reachability→ Test-NetConnection IP -Port 5601
4. Check cookie config       → cookie.secure = false
5. Verify admin password     → curl -k -u admin:password https://127.0.0.1:9200
6. Check dashboard logs      → journalctl -u wazuh-dashboard
7. Check Wazuh API           → security/user/authenticate
8. Fix wazuh.yml credentials
9. Restart dashboard service
10. Login from Incognito mode
```

---

## ✅ Final Root Cause Analysis

After systematically checking every layer, the following were **ruled out**:

- ❌ Network connectivity
- ❌ Port availability
- ❌ Admin password
- ❌ Indexer health
- ❌ Wazuh API health

**Actual root causes identified:**

1. **Cookie/SSL mismatch**
   ```yaml
   opensearch_security.cookie.secure: true
   ```
   conflicting with the dashboard running over plain HTTP.

2. **Wrong API username in `wazuh.yml`**
   ```
   username: wazuh-wui   ❌ (failed authentication)
   username: wazuh        ✅ (with correct API password)
   ```

---

## 📚 Notes

This guide serves as a personal **Wazuh Lab troubleshooting reference**. Following these 10 steps in order should allow rapid diagnosis of the Dashboard login-loop issue in most self-hosted Wazuh deployments.

---

*Last verified on a self-hosted Ubuntu + Wazuh single-node lab setup.*
