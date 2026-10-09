# 🛡️ Cybersecurity Portfolio

Welcome to my **Cybersecurity Portfolio**, developed as part of the **Google Cybersecurity Certificate**.  
This repository showcases practical examples of my technical skills and professional documentation, applied to real-world cybersecurity scenarios.

Each activity demonstrates my ability to utilise industry-standard tools, apply frameworks such as the **NIST Cybersecurity Framework (CSF)**, and produce clear, professional reports for security analysis and incident response.

---

## 📂 Portfolio Content by Course

### **1️⃣ [Play It Safe: Manage Security Risks](https://github.com/JCVadillo/google-cybersecurity-cert-portfolio/tree/main/01_Manage-Security-Risks)**
- **Portfolio Activity:** *Conduct a security audit*  
  Performing a security audit to assess policies, controls, and compliance.

### **2️⃣ [Connect and Protect: Networks and Network Security](https://github.com/JCVadillo/google-cybersecurity-cert-portfolio/tree/main/02_Network-and-Network-Security)**
- **Portfolio Activity:** *Use the NIST Cybersecurity Framework (CSF) to respond to a security incident*  
  Applying the NIST CSF to document the response process for a simulated incident.

### **3️⃣ [Tools of the Trade: Linux and SQL](https://github.com/JCVadillo/google-cybersecurity-cert-portfolio/tree/main/03_Linux-and-SQL)**
- **Portfolio Activity:** *Install and manage software in Linux using APT*  
  Practised installing, verifying, removing, and reinstalling packages like **Suricata** and **tcpdump** using the Linux terminal. Documented the workflow with Markdown, Git, and Linux terminal.

- **Portfolio Activity:** *Navigating and searching files in Linux with basic commands and grep*  
  Navigated directories, inspected files, and used **grep** and pipes to filter logs and text for investigation.

- **Portfolio Activity:** *Manage files and authorization in Linux*  
  Created, removed, moved, and edited files, and managed permissions with `chmod` to enforce least privilege.

- **Portfolio Activity:** *Add and manage users in Linux*  
  Added and removed users, changed file ownership, and managed groups to control system access.

- **Portfolio Activity:** *Get help in the Linux command line*  
  Used **man**, **whatis**, and **apropos** to explore command documentation and differentiate between similar commands.

- **Portfolio Activity:** *Apply filters to SQL queries*  
  Filtered login attempts and employee records using **WHERE**, **AND**, **OR**, **NOT**, and **LIKE** to investigate incidents and plan system updates.

### **4️⃣ [Assets, Threats, and Vulnerabilities](https://github.com/JCVadillo/google-cybersecurity-cert-portfolio/tree/main/04_Assets-Threats-and-Vulneravilities)**
- **Portfolio Activity:** *Score risk based on likelihood and severity*
  Conducted a comprehensive risk assessment for a commercial bank, assigning likelihood and severity values to multiple risks, calculating overall risk scores, and providing detailed notes and justifications based on the NIST Cybersecurity Framework.

- **Portfolio Activity:** *Determine appropriate data handling practices*  
  Analysed a data leak incident caused by excessive access permissions, reviewed **NIST SP 800-53 AC-6 (Least Privilege)**, and recommended control enhancements such as automated access revocation and role-based access controls to improve information privacy.

- **Portfolio Activity:** *Create and compare hash values in Linux*  
  Using SHA-256 hashing and tools like `sha256sum` and `cmp` to verify file integrity and detect subtle modifications.

- **Portfolio Activity:** *Analyze a vulnerable system for a small business*  
  Conducted a structured vulnerability assessment for a publicly accessible e-commerce database server, identifying threat sources and events using **NIST SP 800-30 Rev. 1**, scoring risks by likelihood and severity, and recommending layered remediation controls including vulnerability scanning and an AAA framework.

- **Portfolio Activity:** *Apply the PASTA threat model framework*  
  Conducted an end-to-end threat modelling exercise for a mobile e-commerce application using the **PASTA** framework, identifying business-critical assets, attack vectors, threats, and vulnerabilities. Evaluated application architecture and data flows, analysed attack trees, and proposed security controls including SQL injection prevention, MFA, patch management, and regulatory compliance measures.

### **5️⃣ [Sound the Alarm: Detection and Response](https://github.com/JCVadillo/google-cybersecurity-cert-portfolio/tree/main/05_Sound-the-Alarm-Detection-and-Response)**

- **Portfolio Activity:** *Document an incident with an Incident Handler's Journal*
  Documented a ransomware attack on a U.S. healthcare clinic using a structured **Incident Handler's Journal**, applying the **5 W's framework** to capture threat actor identity, attack vector, timing, scope, and motive. Analyzed the phishing-to-ransomware attack chain and critically evaluated incident response decisions, including the ethical and legal implications of ransom payment in a regulated sector.

- **Portfolio Activity:** *Investigate a suspicious file hash with VirusTotal and the Pyramid of Pain*
  Analysed a suspicious file artefact using **VirusTotal** to determine malicious verdict through multi-vendor detection and sandbox behavioural data. Mapped identified indicators of compromise — hashes, IP addresses, domains, network/host artifacts, and TTPs — across the **Pyramid of Pain** framework, and extracted **MITRE ATT&CK** tactics to strengthen detection rules and differentiate low-value from high-value indicators.

- **Portfolio Activity:** *Document a threat intelligence investigation in an Incident Handler's Journal*
  Combined threat intelligence analysis and structured incident documentation by investigating a suspicious file hash via **VirusTotal** and recording findings as a formal **Incident Handler's Journal** entry using the 5 W's. Mapped malicious behaviors across the **Pyramid of Pain** and aligned identified tactics with the **MITRE ATT&CK** framework to support the Detection and Analysis phase of the NIST Incident Response Lifecycle.

- **Portfolio Activity:** *Deploy and configure Suricata IDS on a virtualized ARM64 environment*
  Deployed **Suricata 8.0.6** as a network-based Intrusion Detection System on a dedicated Ubuntu 26.04 LTS VM running on **UTM (QEMU)** on a Mac M1, following the principle of least functionality with separate VMs per security tool. Configured the network interface, downloaded the **Emerging Threats Open** ruleset via `suricata-update`, authored a custom ICMP detection rule, and verified live alert generation through real-time CLI log analysis of `fast.log` and `eve.json`.

- **Portfolio Activity:** *Examine alerts, logs, and rules with Suricata*
  Analysed the anatomy of a custom **Suricata rule**, ran Suricata in offline mode against a `.pcap` capture file, and examined generated alerts in `fast.log`. Parsed and filtered the structured `eve.json` event log using `jq` to extract timestamps, flow IDs, destination IPs, HTTP metadata, and alert signatures — reinforcing the distinction between IDS and IPS operational modes and the role of `flow_id` as a correlation key for network event reconstruction.

- **Portfolio Activity:** *Deploy a Wazuh SIEM on Apple Silicon (ARM64)*
  Adapted the official Wazuh lab guide — designed exclusively for x86_64 — to deploy a fully functional **Wazuh SIEM** (Indexer, Manager, and Dashboard) on an Apple Silicon M1 Mac using **UTM** with a native ARM64 Ubuntu Server VM. Configured a single-node installation, ingested **109,872 log events** via **Filebeat**, created an index pattern in Wazuh Discover, and troubleshot multiple real installation failures — including cluster initialisation errors, silent service failures, and file permission issues.

### **6️⃣ [Automate Cybersecurity Tasks with Python](https://github.com/JCVadillo/google-cybersecurity-cert-portfolio/tree/main/06_Automate-Cybersecurity-Tasks-with-Python)**
- **Portfolio Activity:** *Create a Python algorithm to update an IP allow list*  
  Developed a reusable **Python** function to read and parse a text file of allowed IP addresses, remove entries listed for access revocation, and rewrite the updated file. Used file handling, **split()**, **join()**, loops, and conditionals, then read the file again to verify the changes.

- **Portfolio Activity:** *Debug Python code*  
  Practised identifying and resolving **syntax errors**, **exceptions**, and **logic errors** in Python code for user validation, IP allow list updates, and security patch scheduling. Corrected punctuation, indentation, variable names, method calls, and list indices, then reran the code to verify the expected results.

### **7️⃣ Put It to Work: Prepare for Cybersecurity Jobs** - *In progress*
- **Portfolio Activity:** *Create a professional incident response report*  
  Compiling a comprehensive, professional incident response report integrating all prior skills.

---

## 🔧 Tools and Technologies
- **Systems & Networks:** Linux, TCP/IP, Firewalls, VPNs  
- **Monitoring & Response:** Splunk (SIEM), Log Analysis  
- **Risk Management:** NIST CSF, Risk Assessments  
- **Technical Analysis:** Wireshark, Nessus (Vulnerability Scanning)  
- **Automation:** Python, Bash  
- **Databases:** SQL for log and data analysis  

---

## 🎯 Portfolio Purpose
This portfolio demonstrates my hands-on preparation in cybersecurity through:
- Risk identification and mitigation  
- Incident detection and response  
- Vulnerability scanning and reporting  
- SIEM-based monitoring and log analysis  
- Security automation with Python  

---

## 📬 Contact
- [LinkedIn](https://www.linkedin.com/in/juan-carlos-vadillo/)  
- [Email](mailto:jc.vadillo87@gmail.com)  

