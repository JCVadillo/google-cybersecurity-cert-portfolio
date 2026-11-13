### Analyzing System Event Logs with Gemini Notebook
*Google Cybersecurity Certificate – Course 07: Prepare for Cybersecurity Jobs* *(Portfolio Activity – Analyze Event Logs with Gemini Notebook)*

--------------------------------------------------------------------------------

#### 🎯 Lab Summary
In this activity, I acted as a junior security analyst to review and analyze system event logs (`system_activity_log_2025-07-30.txt`) using **Gemini Notebook** to identify potential security incidents, malicious insider threats, and authentication anomalies.

The main tasks were to:
* Review system log entries to categorize authentication, file operation, and system health events
* Identify out-of-the-ordinary events, failed login attempts, and policy violations
* Evaluate security risks and rank log entries by severity level (Critical, High, Medium)
* Document findings and formulate incident response recommendations for SOC escalation

--------------------------------------------------------------------------------

#### ⚙️ Scenario Overview
As an entry-level security analyst responsible for network monitoring and incident investigation, I reviewed log data flagged by system monitoring tools. The log dataset captures activity from July 30, 2025, detailing user logins, file access actions, internal communications, and automated system maintenance. Utilizing Gemini Notebook, I analyzed event patterns, isolated critical anomalies, and structured tactical containment steps.

--------------------------------------------------------------------------------

#### 📓 Event Log Analysis & Investigation

##### Log Data Analyzed (`system_activity_log_2025-07-30.txt`)

| Timestamp | User | Source IP | Event Type | Details | Status / Classification |
| --- | --- | --- | --- | --- | --- |
| 2025-07-30 08:00:15 | admin | 192.168.1.1 | Login_Success | User 'admin' logged in from internal network | 🟢 Benign |
| 2025-07-30 08:05:30 | John.Doe | 192.168.1.5 | File_Access | Opened: /documents/report_draft.docx | 🟢 Benign |
| 2025-07-30 08:10:45 | jane.smith | 192.168.1.10 | Email_Sent | To: allstaff@company.com, Subject: Important Update | 🟢 Benign |
| 2025-07-30 08:15:00 | guest | 10.0.0.100 | Login_Failed | User 'guest' attempted login from unknown IP (1 attempt) | 🟡 Suspicious |
| 2025-07-30 08:15:05 | guest | 10.0.0.100 | Login_Failed | User 'guest' attempted login (2 attempts) | 🟡 Suspicious |
| 2025-07-30 08:15:10 | guest | 10.0.0.100 | Login_Failed | User 'guest' attempted login (3 attempts) | 🟡 Suspicious |
| 2025-07-30 08:20:20 | John.Doe | 192.168.1.5 | File_Access | Copied: /finance/budget_2026_final.xlsx to /public_share | 🟠 High Risk |
| 2025-07-30 08:25:35 | system | N/A | Service_Status | Web server (Apache) is running. | 🟢 Benign |
| 2025-07-30 08:30:40 | admin | 203.0.113.25 | Login_Success | User 'admin' logged in from external IP (unusual location) | 🔴 Critical Risk |
| 2025-07-30 08:35:50 | mary.jones | 192.168.1.12 | File_Deletion | Deleted: /personal/vacation_photos.jpg | 🟢 Benign |
| 2025-07-30 08:40:05 | system | N/A | Software_Update | Antivirus definitions updated successfully | 🟢 Benign |

--------------------------------------------------------------------------------

##### 🚨 Security Risk Assessment & Key Findings

| Severity Level | Event Details | Risk Rationale & Impact Analysis |
| --- | --- | --- |
| 🔴 **Critical Severity** | **Admin Login from External IP** (`203.0.113.25` at `08:30:40`) | Administrative accounts possess elevated system access. Successful authentication from an unapproved external IP address indicates compromised credentials, Account Takeover (ATO), or unauthorized remote access. |
| 🟠 **High Severity** | **Confidential Financial File Exposure** (`John.Doe` at `08:20:20`) | Transferring `/finance/budget_2026_final.xlsx` to `/public_share` violates Data Loss Prevention (DLP) protocols. Exposing unreleased financial records in a shared folder introduces significant risk of insider threat or accidental data leakage. |
| 🟡 **Medium Severity** | **Repeated Failed Login Attempts** (`10.0.0.100` at `08:15:00 - 08:15:10`) | Three failed login attempts for the `guest` account within 10 seconds from an unknown IP address suggest automated password guessing, brute-force activity, or external network reconnaissance. |

--------------------------------------------------------------------------------

#### 🧠 Reflections / Notes
* **Event Correlation in Log Analysis:** Isolated events may appear benign, but correlating privileged user logins with external IP addresses or unexpected file transfers exposes critical attack vectors.
* **Privileged Identity Management:** Administrative access must be strictly constrained using Multi-Factor Authentication (MFA), IP whitelisting, and real-time geographic anomaly alerts.
* **Data Loss Prevention (DLP) Controls:** Strict file system permissions must be enforced to prevent users from moving restricted corporate data into public network shares.

--------------------------------------------------------------------------------

#### 📚 Key Skills Demonstrated
* System log parsing and operational event categorization across security domains
* Identification of Indicators of Compromise (IoCs) and anomalous behavior detection
* Severity-based risk assessment and threat prioritization for enterprise environments
* Documentation of security analysis following industry portfolio standards

--------------------------------------------------------------------------------

*This activity demonstrates the ability to analyze system activity logs, isolate critical security anomalies, evaluate organizational risk, and document structured findings for a cybersecurity portfolio.*

