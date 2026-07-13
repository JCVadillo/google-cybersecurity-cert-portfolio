# Analyzing Indicators of Compromise with VirusTotal and the Pyramid of Pain
*Google Cybersecurity Certificate – Course 05: Sound the Alarm: Detection and Response*
*(Portfolio Activity – Investigate a Suspicious File Hash)*

---

## 🎯 Lab Summary

In this activity, I analyzed a suspicious file artifact using **VirusTotal** to investigate threat intelligence data and captured key indicators of compromise (IoCs) using the **Pyramid of Pain** framework.

The main tasks were to:
- Investigate a specific file hash using VirusTotal to determine if it is malicious
- Analyze multi-vendor detection verdicts and threat intelligence metadata
- Extract distinct types of indicators of compromise from sandbox execution reports
- Map findings into the Pyramid of Pain template to understand the operational impact on the threat actor

---

## ⚙️ Scenario Overview

As a security analyst, I received a security alert regarding an unknown file executed on an enterprise endpoint. To assess the scope of the potential security incident and build technical context around the alert, I took the file's cryptographic hash value and analyzed it using **VirusTotal**. By exploring its multi-vendor detection logs, behavior logs, relations tab, and community score, I verified its malicious status and extracted actionable threat intelligence to help enhance detection rules and defensive capabilities within the organization.

---

## 📓 Threat Intelligence Analysis

### Cryptographic Verification

| Field | Details |
|---|---|
| **File Status** | 🔴 Malicious |
| **Reasoning** | The file hash was heavily flagged by a significant majority of third-party security vendors and antivirus engines on VirusTotal, confirming a clear malicious classification rather than a false positive. |

---

### 🧱 The Pyramid of Pain Mapping

| Pyramid Level | Indicator Type & Observed Findings |
|---|---|
| **TTPs** | **Tactics, Techniques, and Procedures:** Execution via Command and Scripting Interpreter (MITRE ATT&CK T1059), Registry modification for persistence, and Process Injection into legitimate system processes. |
| **Tools** | **Software used by the attacker:** The malware utilized administrative utilities and command-line execution tools (such as `cmd.exe` or custom web request utilities) to run scripts and download additional payloads. |
| **Network/Host Artifacts** | **Traces left on systems/networks:** Creation of specific registry keys under `Software\Microsoft\Windows\CurrentVersion\Run`, drops of drop-files in local temporary directories, and distinct User-Agent strings used in HTTP POST requests. |
| **Domain Names** | **Malicious domains contacted:** Known command-and-control (C2) servers and staging domains (extracted from the *Relations* or *Behavior* sandbox outputs) used to retrieve configurations or drop secondary payloads. |
| **IP Addresses** | **Remote endpoints contacted:** Distinct external IPv4 addresses associated with the malware’s callbacks, which were flagged by threat feeds as hosted on malicious infrastructure. |
| **Hash Values** | **Cryptographic uniquely identifying strings:** The unique SHA-256 / MD5 hash analyzed during the investigation, which serves as a static signature of this specific malware sample. |

---

## 🧠 Reflections / Notes

- This activity highlighted that **threat intelligence tools like VirusTotal are critical** for providing immediate context to automated alerts, transforming an abstract string (a hash) into a fully documented behavioral signature.
- I learned how to apply the **Pyramid of Pain practically** to differentiate between low-value and high-value indicators; blocking a hash value is trivially easy for a security team but trivial for an attacker to change, whereas targeting their TTPs forces them to rewrite their operation.
- Navigating the **Behavior tab and Sandbox reports** demonstrated the value of dynamic analysis, showing how a file acts during execution (file drops, registry modifications, process injection) rather than just what its code looks like statically.
- A critical operational takeaway is the **VirusTotal data sharing warning**: uploading actual proprietary files can expose sensitive corporate data to the public threat intelligence community, meaning analysts must prioritize searching by pre-computed hashes whenever possible.
- This investigation directly supports the **Detection and Analysis phase of the NIST Incident Response Lifecycle**, showing how validation of indicators prevents alert fatigue and ensures remediation efforts target verified threats.

---

## 📚 Key Skills Demonstrated

- Utilization of open-source threat intelligence platforms (**VirusTotal**) to query and interpret file reputations
- Practical mapping and structuring of technical intelligence across the **Pyramid of Pain** framework
- Analysis of sandbox evaluation summaries to identify distinct malicious artifacts (registry keys, dropped files, network calls)
- Extraction of **MITRE ATT&CK tactics and techniques** from operational behavior logs to map threat patterns
- Differentiating between static indicators of compromise (hashes, IPs) and behavioral indicators (TTPs)
- Alignment of investigative findings with standard security orchestration workflows to enhance defensive monitoring rules

---

*This activity demonstrates the ability to investigate a suspicious cryptographic file artifact using threat intelligence repositories, analyze sandbox execution behavior, map diverse indicators of compromise using the Pyramid of Pain, and extract actionable data to strengthen corporate perimeter defenses.*
