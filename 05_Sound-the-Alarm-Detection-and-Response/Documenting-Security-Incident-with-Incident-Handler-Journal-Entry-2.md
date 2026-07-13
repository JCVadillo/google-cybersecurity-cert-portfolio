# Documenting a Security Incident with an Incident Handler's Journal
*Google Cybersecurity Certificate – Course 05: Sound the Alarm: Detection and Response*
*(Portfolio Activity – Documenting and Investigate a Suspicious File Hash)*

---

## 🎯 Lab Summary

In this activity, I acted as a security analyst to investigate a suspicious file hash using **VirusTotal**, subsequently documenting my findings inside an **Incident Handler's Journal** using the 5 W's framework and threat intelligence concepts.

The main tasks were to:
- Analyze an artifact hash using a threat intelligence platform (VirusTotal)
- Determine whether the file was malicious based on security vendor detection verdicts
- Extract crucial Indicators of Compromise (IoCs) across different levels of the Pyramid of Pain
- Document a complete journal entry to reflect proper incident context and analysis

---

## ⚙️ Scenario Overview

As a security analyst, a high-priority alert was received indicating that a suspicious file had been downloaded or executed on an internal system. To assess the threat level and gather context, the unique cryptographic file hash (SHA-256) of the artifact was extracted and researched using VirusTotal. The goal was to identify if the asset was compromised, investigate technical indicators (such as malicious connections or tools utilized), and log the analysis formally for the incident response lifecycle.

---

## 📓 Incident Handler's Journal

### Entry #2

| Field     | Details          |
|-----------|------------------|
| **Date** | July 13, 2026    |
| **Entry** | #2               |

---

### 📝 Description
Investigating a Suspicious File Hash via Threat Intelligence (VirusTotal)

---

### 🛠️ Tool(s) Used
- VirusTotal (Threat Intelligence Platform)
- MITRE ATT&CK® Framework Matrix

---

### 🔍 The 5 W's

| W | Details |
|---|---------|
| **Who** | A malicious actor or threat group deploying standardized malware families (as flagged by multiple security vendors under classifications like Trojan, Ransomware, or Downloader). |
| **What** | An alert was triggered by a suspicious file download/execution. Threat intelligence analysis confirmed that the file hash is highly malicious, designed to establish network persistency, drop secondary artifacts, and utilize specific adversarial tools. |
| **When** | The investigation took place immediately following the detection alert during my shift on July 13, 2026. |
| **Where** | The initial download was observed on an internal corporate endpoint (host), while the malware itself attempted to communicate out to external malicious infrastructure (IP/Domain). |
| **Why** | The file hash analysis revealed clear intent to compromise system integrity. The malware's behavior includes executing specific tactics and techniques mapped to MITRE ATT&CK, aiming to bypass security controls, drop further payloads, or communicate with a Command and Control (C2) server. |

---

### 💬 Additional Notes
1. **Malicious Verdict:** The file hash is explicitly malicious based on overwhelming multi-vendor detection and low/negative community scores.
2. **Pyramid of Pain Mapping:** - *Hash (Trivial):* The unique SHA-256 string can be immediately added to the blocklist.
   - *Network/Host Artifacts (Easy/Simple):* Discovered dropped temporary files, altered registry paths, and outbound connections/domains used for beaconing.
   - *Tools/TTPs (Challenging/Tough):* Discovered automated execution techniques, obfuscation tools, and subverted system processes mapped to specific MITRE ATT&CK IDs.

---

## 🧠 Reflections / Notes

- **The Power of Context:** Investigating an isolated alert becomes significantly more actionable when paired with threat intelligence. VirusTotal allowed me to immediately pivot from a raw hash to knowing exactly what behaviors, files, and domains the malware interacts with.
- **Applying the Pyramid of Pain:** While blocking a file hash is simple, it is also trivial for an attacker to modify one bit of code to generate a completely new hash. Moving higher up the pyramid—identifying network artifacts and the threat actor's TTPs—provides far more durable and long-lasting defensive posture.
- **Public Sandbox Safety:** A key takeaway from using open investigative platforms is operational security (OpSec). Uploading raw proprietary files can leak sensitive internal data to the public; using unique hashes ensures an analyst safely fetches existing threat intel without exposing internal assets.
- **Integration with MITRE ATT&CK:** Identifying the exact tactics and techniques utilized by the malware (e.g., Discovery, Execution, or Persistence) allows the security team to evaluate if existing endpoint detection rules (EDR) and security controls are robust enough to catch similar behavioral indicators in the future.

---

## 📚 Key Skills Demonstrated

- **Threat Intelligence Analysis:** Utilizing VirusTotal to interpret security vendor verdicts, analyze behavioral sandbox patterns, and read community intelligence indicators.
- **Incident Response Documentation:** Applying structured log maintenance within an Incident Handler's Journal to preserve context for the security team.
- **Indicators of Compromise (IoCs) Identification:** Isolating and cataloging diverse categories of threat indicators (hashes, network domains, file system behaviors).
- **Framework Alignment:** Mapping observed malware behavior directly against the MITRE ATT&CK framework matrix for tactical defensive engineering.
- **Risk Assessment:** Differentiating between benign, suspicious, and highly malicious software based on empirical multi-engine correlation data.

---

*This activity demonstrates the capability to leverage open-source threat intelligence infrastructure to validate alerts, unpack complex malware behavior, map threats against industry frameworks, and document findings to enhance an enterprise incident response posture.*
