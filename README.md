# GRC Portfolio Project: SOC Detection & Incident Analysis (SIEM Simulation)

**Date:** May 4, 2026 [cite: 359]  
**Location:** Armadale, Western Australia [cite: 178]  
**Author:** Noel Fuentes  
**Environment:** SIEM Simulation (Splunk/ELK) [cite: 361]

## 1. Project Overview: The "What"
This lab was a **Blue Team** simulation focused on the defensive side of cybersecurity[cite: 358]. The objective was to identify the "breadcrumbs" left behind by a hacker who used social engineering to deliver a malicious payload[cite: 358]. In a GRC context, this demonstrates the effectiveness of **Detective Controls** and the critical need for **Security Awareness Training**[cite: 384, 424].

## 2. Forensic Methodology: The "How"
The investigation followed a four-phase forensic lifecycle to identify the root cause of a suspicious alert[cite: 360]:

* **Phase 1: Alert Trigger:** The investigation began with a "Suspicious Process" alert on a specific workstation[cite: 362]. Log queries were used to identify the user's activity immediately preceding the alert[cite: 363].
* **Phase 2: Email & Attachment Analysis:** Social engineering was identified via a log entry for an email with the subject "Overdue Invoice"[cite: 364]. While named `Invoice.pdf`, metadata revealed it was actually a disguised executable (likely a `.lnk` or `.hta` file)[cite: 365, 366].
* **Phase 3: Process Tree Investigation:** Analysis showed `explorer.exe` (the user interaction) spawning `powershell.exe` instead of a PDF reader[cite: 369]. The PowerShell command contained a Base64 encoded string, which was de-obfuscated using **CyberChef** to reveal the reverse shell script[cite: 371, 372].
* **Phase 4: Network Forensics:** Network logs (Sysmon Event ID 3) confirmed an outbound connection from the PowerShell process to an external attacker IP on a non-standard port (e.g., 4444)[cite: 374, 375].


---

## 3. Business Impact & Risk Analysis: The "Why"
From a business perspective, a successful reverse shell results in unauthorized remote access, leading to significant risks[cite: 376, 382]:

| CIA Pillar | Impact of Breach | Business "Why" |
| :--- | :--- | :--- |
| **Confidentiality** | **High Risk**: Malicious actors gain the ability to exfiltrate sensitive company data[cite: 382, 383]. | **Data Theft**: Leads to competitive disadvantage, legal liabilities, and regulatory fines[cite: 192]. |
| **Integrity** | **High Risk**: Attackers can modify or delete financial records and system configurations[cite: 193, 194]. | **Unauthorized Modification**: Erodes customer trust and leads to poor business decision-making[cite: 194]. |
| **Availability** | **Moderate Risk**: The attacker could disable the system or launch ransomware[cite: 195, 444]. | **System Destruction**: Downtime results in lost revenue and high recovery costs[cite: 196]. |

---

## 4. Compliance & Framework Mapping

### 🇦🇺 ASD Essential Eight (E8)
* **User Application Hardening (FAILED):** The environment allowed PowerShell to execute unverified scripts from the user's directory[cite: 381, 382]. Hardening would require restricting PowerShell to "Constrained Language Mode"[cite: 382].
* **Restrict Administrative Privileges:** In a real-world scenario, if the user was running as a local admin, the "blast radius" of this script would increase significantly[cite: 161, 162].

### 🇦🇺 ASD Cyber Security Principles (March 2026 Update)
* **System Exposure Minimisation (FAILED):** This principle recommends minimizing information available to attackers; however, the lack of email filtering allowed the malicious attachment to reach the end-user[cite: 164, 165, 200].

### 🌐 International Standards (ISO & NIST)
* **ISO/IEC 27001:2022 Control A.8.27 (Secure Routing):** Failed to prevent outbound traffic to known-malicious or non-standard ports[cite: 203].
* **NIST CSF 2.0 (DE.AE):** This lab demonstrates the **Detect** function by identifying and analyzing a security event through SIEM alerting and log correlation[cite: 493].
* **OWASP Top 10 (2025 Edition):**
    * **A01: Broken Access Control:** The initial delivery via phishing exploited a lack of strict access controls on email attachments[cite: 381, 484].
    * **A05: Injection:** The use of malicious PowerShell scripts to execute unintended system commands[cite: 381, 489].

