# Network-Traffic-Analysis-for-Threat-Hunting-on-OpenStack-Cloud-Environment

## Network Traffic Analysis: OpenStack Cloud Breach (Vantage Sherlock, HTB)

## 📌 Project Overview
This project is a detailed forensic investigation of a compromised OpenStack cloud environment. By analyzing network traffic (`.pcap` files), I mapped an adversary's journey from initial reconnaissance to full data exfiltration and the establishment of persistence using the **Cyber Kill Chain** framework.

**Scenario:** On July 1, 2025, unauthorized access was detected on the `cloud.vantage.tech` subdomain. The attacker bypassed authentication, compromised OpenStack API credentials, and exfiltrated sensitive PII from Swift object storage.

---

## 🛡️ Executive Summary
* **Target:** OpenStack Infrastructure-as-a-Service (IaaS).
* **Attack Vector:** Subdomain fuzzing followed by Credential Stuffing/Brute Force.
* **Impact:** Exfiltration of `user-details.csv` containing 28 PII records.
* **Persistence:** Unauthorized creation of a new administrative user `jellibean`.

---

## 🕵️ Technical Investigation & Kill Chain Mapping

### 1. Reconnaissance
The attacker used **ffuf v2.1.0** to perform directory and subdomain enumeration.
* **Finding:** Identified the `cloud.vantage.tech` dashboard.
* **Artifact:** HTTP GET requests with the User-Agent `Fuzz Faster U Fool v2.1.0-dev`.

### 2. Exploitation
The attacker targeted the Horizon login portal.
* **Action:** Successfully authenticated using admin credentials at **09:40:07 UTC**.

### 3. Installation & Credential Harvesting
Once inside the dashboard, the attacker navigated to **Project > API Access**.
* **Artifact:** Downloaded `admin-openrc.sh`.
* **Impact:** This file contained the OS_PROJECT_ID and credentials needed to interact directly with the OpenStack API.

### 4. Actions on Objectives (Exfiltration)
The attacker used the stolen `X-Auth-Token` to interact with the **Swift Object Storage** API.
* **Target Containers:** `dev-files`, `employee-data`, and `user-data`.
* **Exfiltration:** Downloaded `user-details.csv` at **09:45:23 UTC**.

### 5. Persistence
To maintain long-term access, the attacker issued a `POST` request to the `/identity/v3/users` endpoint
* **Account Created:** `jellibean`.
* **Password:** `P@$$word`.

---

## 🔍 Threat Hunting Indicators (IOCs)
| Type | Value | Description |
| :--- | :--- | :--- |
| **IP Address** | `134.209.71.220` | Attacker Source IP |
| **User-Agent** | `ffuf/2.1.0` | Discovery Tooling |
| **Filename** | `user-details.csv` | Exfiltrated Sensitive Data |
| **Persistence** | `jellibean` | Rogue Admin Account |

---

## 💡 Recommendations & Remediation
* **Identity:** Implement Multi-Factor Authentication (MFA) for the Horizon dashboard and API access.
* **Secret Management:** Rotate all API credentials and move away from plaintext `.sh` configuration files to a secure vault system.
* **Monitoring:** Enable real-time alerts for any `POST` requests to the Keystone API involving user creation.
* **Network:** Restrict OpenStack API endpoints to internal IP ranges or VPN access only.

---

## 🛠️ Tools Used
* **Wireshark:** Network traffic analysis and HTTP stream carving.

---
*This investigation was completed as part of the **Vantage Sherlock** on Hack The Box.*

<img width="1330" height="620" alt="Screenshot (1714)" src="https://github.com/user-attachments/assets/7d2f5698-07c7-40df-9a09-6043e9ffd590" />
https://labs.hackthebox.com/achievement/sherlock/2413013/1162
