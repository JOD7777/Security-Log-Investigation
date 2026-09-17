# 🔎 Security Log Investigation

![Cybersecurity](https://img.shields.io/badge/Domain-Cybersecurity-red)
![SOC](https://img.shields.io/badge/Focus-SOC%20Investigation-blue)
![Python](https://img.shields.io/badge/Python-3.x-yellow)
![Dataset](https://img.shields.io/badge/Dataset-Synthetic-green)
![Status](https://img.shields.io/badge/Status-Completed-success)

## 📌 Overview

This project demonstrates a beginner-friendly **security log investigation** using a synthetic authentication dataset.

The investigation focuses on identifying suspicious authentication behavior, reconstructing an incident timeline, separating evidence from assumptions, and recommending defensive response actions.

The project follows a SOC-style investigation workflow.

---

## 🎯 Objectives

* Normalize authentication log data
* Analyze authentication events
* Detect password-spraying behavior
* Detect brute-force activity
* Identify unusual privileged access
* Reconstruct an incident timeline
* Separate evidence from assumptions
* Recommend defensive response actions
* Document findings in an incident report

---

## 🧰 Technologies & Tools

* **Python 3**
* **Pandas**
* **Matplotlib**
* **Jupyter Notebook**
* **CSV**
* **Git & GitHub**

---

## 📂 Project Structure

```text
Security-Log-Investigation/
│
├── data/
│   └── auth_log.csv
│
├── investigation/
│   └── investigation.ipynb
│
├── evidence/
│   └── timeline.csv
│
├── report/
│   └── incident_report.pdf
│
├── README.md
└── LICENSE
```

---

## 🔍 Investigation Methodology

The investigation followed these stages:

```text
Raw Authentication Logs
          ↓
Data Normalization
          ↓
Authentication Analysis
          ↓
Detection Rules
          ↓
Suspicious Activity Identification
          ↓
Incident Timeline
          ↓
Evidence vs Assumptions
          ↓
Risk Assessment
          ↓
Response Recommendations
```

---

## 1️⃣ Log Normalization

The following fields were normalized:

| Field       | Description                  |
| ----------- | ---------------------------- |
| `timestamp` | Event time converted to UTC  |
| `source_ip` | Source IP address            |
| `username`  | Account involved             |
| `action`    | Authentication/access action |
| `result`    | Success or failure           |
| `device`    | Device information           |

Normalization makes the data easier to analyze consistently.

---

## 2️⃣ Password Spraying Detection

A password-spraying detection rule identifies a source IP attempting authentication against multiple accounts.

### Detection logic

```python
failed.groupby("source_ip")["username"].nunique()
```

A source targeting three or more unique accounts was flagged for investigation.

### Finding

```text
Source IP: 185.220.101.10

Accounts targeted:
- alice
- bob
- charlie
- david
- emma
```

This behavior is **consistent with a password-spraying pattern**.

---

## 3️⃣ Brute-Force Detection

Brute-force activity was identified by looking for repeated authentication failures against the same account.

### Detection logic

```python
failed.groupby(
    ["source_ip", "username"]
).size()
```

### Finding

```text
Source IP: 10.10.10.50
Account: alice
Failed attempts: 5
```

A successful login occurred shortly afterward, making the sequence important for further investigation.

---

## 4️⃣ Unusual Access Investigation

The investigation examined events occurring after suspicious authentication.

Observed sequence:

```text
08:20:02 → Failed login
08:20:04 → Failed login
08:20:06 → Failed login
08:20:08 → Failed login
08:20:10 → Failed login
08:21:02 → Successful login
08:22:15 → VPN access
08:24:11 → Admin panel access
```

The logs establish this sequence of events.

However, the available data does **not independently prove** that the account was compromised or that the access was unauthorized.

---

## 5️⃣ Service Account Investigation

The `service_account` generated the following activity:

```text
08:30:00 → Successful login
08:30:20 → Database administration access
08:31:02 → Configuration access
08:32:10 → Logout
```

This activity should be reviewed against expected service-account behavior and approved administrative activity.

---

# 🕒 Incident Timeline

| Time UTC    | Event                                                   |
| ----------- | ------------------------------------------------------- |
| 08:15:01    | Authentication failures begin against multiple accounts |
| 08:15–08:16 | Multiple accounts targeted from 185.220.101.10          |
| 08:20:02    | Repeated failures begin against alice                   |
| 08:20:10    | Fifth failed authentication attempt                     |
| 08:21:02    | Successful authentication as alice                      |
| 08:22:15    | VPN access                                              |
| 08:24:11    | Admin-panel access                                      |
| 08:30:00    | Service account successfully authenticates              |
| 08:30:20    | Database administration access                          |
| 08:31:02    | Configuration access                                    |
| 08:32:10    | Service account logs out                                |

---

# 🧾 Evidence vs. Assumptions

## Direct Evidence

The dataset directly demonstrates:

* Multiple accounts were targeted from `185.220.101.10`
* Five failed authentication attempts occurred against `alice`
* A successful authentication followed those failures
* `alice` accessed VPN resources
* `alice` accessed the admin panel
* `service_account` accessed database administration
* `service_account` accessed configuration resources

## Assumptions / Hypotheses

The following require additional evidence:

* The password-spraying activity was malicious
* The `alice` account was compromised
* The admin-panel access was unauthorized
* The service account was compromised or misused

These should remain hypotheses until corroborated by additional security telemetry.

---

# ⚠️ Risk Assessment

| Finding                                           | Risk |
| ------------------------------------------------- | ---- |
| Password spraying                                 | High |
| Brute-force activity                              | High |
| Successful login after repeated failures          | High |
| Privileged access after suspicious authentication | High |
| Unusual service-account activity                  | High |

Risk assessments are based on the potential security impact and the observed authentication patterns in this synthetic dataset.

---

# 🚨 Recommended Response Actions

## Immediate Actions

1. Review the `alice` account.
2. Reset credentials if compromise is suspected.
3. Revoke active sessions.
4. Review MFA activity.
5. Investigate admin-panel activity.
6. Review service-account activity.
7. Preserve relevant authentication and application logs.

## Detection Improvements

* Alert when one IP targets multiple accounts.
* Alert when repeated authentication failures are followed by success.
* Monitor privileged access after suspicious authentication.
* Monitor service accounts for unusual behavior.
* Centralize authentication logs in a SIEM.

## Preventive Controls

* Multi-factor authentication
* Authentication rate limiting
* Account throttling
* Strong password policies
* Least-privilege access
* Service-account restrictions
* Continuous security monitoring

---

# 🧪 Running the Investigation

Clone the repository:

```bash
git clone https://github.com/YOUR-USERNAME/Security-Log-Investigation.git
cd Security-Log-Investigation
```

Install the required Python packages:

```bash
pip install pandas matplotlib jupyter
```

Start Jupyter Notebook:

```bash
jupyter notebook
```

Open:

```text
investigation/investigation.ipynb
```

Run all cells to reproduce the investigation.

---

# 📊 Expected Findings

The investigation should identify:

```text
[!] Password Spraying
    Source: 185.220.101.10
    Targets: 5 accounts

[!] Brute Force
    Source: 10.10.10.50
    Target: alice
    Failures: 5

[!] Suspicious Authentication Sequence
    Failed attempts → Successful login → VPN → Admin panel

[!] Service Account Activity
    Login → Database Admin → Configuration
```

---

# 📄 Investigation Report

The detailed incident report is available here:

```text
report/incident_report.pdf
```

The report contains:

* Executive summary
* Investigation scope
* Detection findings
* Incident timeline
* Evidence vs. assumptions
* Risk assessment
* Recommended response
* Conclusion

---

# 🔐 Security & Privacy

This project uses **synthetic authentication data** created for educational purposes.

No real:

* Passwords
* API keys
* Session tokens
* Credentials
* Personal information
* Organizational logs

should be uploaded to this repository.

If real logs are used in future investigations, sensitive information must be appropriately redacted before publication.

---

# ⚠️ Disclaimer

This project is intended for cybersecurity education, SOC training, and defensive security analysis.

The findings demonstrate investigation techniques using synthetic data and should not be interpreted as evidence of a real-world security incident.

---

# 👨‍💻 Author

**Supriyo Malik**

Cybersecurity & Advanced Networking Student

### Areas of Interest

* SOC Operations
* Cybersecurity
* Network Security
* Threat Detection
* Incident Response
* VAPT
* Ethical Hacking
* Security Monitoring

---

# 📜 License

This project is released under the MIT License.

See [`LICENSE`](LICENSE) for details.
