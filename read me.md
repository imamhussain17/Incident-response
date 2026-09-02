# Vulnerability Assessment & Penetration Testing (VAPT) Report

## Simulated Security Assessment of Metasploitable 2

**Security Analyst:** Imam Hussain Shaik
**Assessment Date:** August 27, 2026
**Assessment Type:** Vulnerability Assessment & Penetration Testing (VAPT)
**Target:** Metasploitable 2
**Attacker Platform:** Kali Linux
**Environment:** Isolated Host-Only Home Lab
**Classification:** Simulated — Educational / Portfolio Use

---

# 1. Executive Summary

This report documents a controlled **Vulnerability Assessment and Penetration Testing (VAPT)** engagement conducted against a deliberately vulnerable Metasploitable 2 virtual machine.

The objective of the assessment was to identify exposed services, enumerate software versions, identify security vulnerabilities, validate the exploitability of identified vulnerabilities, determine their potential impact, and provide appropriate remediation recommendations.

During reconnaissance and service enumeration, multiple network services were identified on the target system, including FTP, SSH, Telnet, SMTP, HTTP, MySQL, PostgreSQL, and Samba.

The FTP service was identified as **vsftpd 2.3.4**, a version associated with the **VSFTPD 2.3.4 Backdoor vulnerability (CVE-2011-2523)**.

The vulnerability was subsequently validated using the Metasploit Framework. Successful exploitation resulted in a remote session with **root-level privileges**, demonstrating that the vulnerable service could lead to complete compromise of the target host.

### Overall Risk Rating

**CRITICAL**

The finding is considered critical because successful exploitation resulted in unauthorized remote access with root-level privileges.

> **Assessment limitation:** This assessment was performed exclusively against an intentionally vulnerable Metasploitable 2 virtual machine in an isolated home-lab environment. No production, third-party, or external systems were targeted.

---

# 2. Assessment Objectives

The primary objectives of this VAPT assessment were to:

* Identify the target system and exposed services.
* Perform network and service enumeration.
* Identify software versions running on exposed services.
* Discover known vulnerabilities associated with identified services.
* Validate the exploitability of identified vulnerabilities.
* Determine the level of access obtained after exploitation.
* Assess the security impact of successful exploitation.
* Document technical evidence for identified vulnerabilities.
* Provide remediation recommendations.
* Define a retesting methodology for validating remediation.

---

# 3. Scope

## 3.1 In-Scope Assets

| Asset                  | Details                                        |
| ---------------------- | ---------------------------------------------- |
| Target System          | Metasploitable 2                               |
| Target IP              | `192.168.15.129`                               |
| Attacker System        | Kali Linux                                     |
| Attacker IP            | `192.168.15.137`                               |
| Network                | Isolated host-only network                     |
| Assessment Perspective | External network perspective                   |
| Testing Type           | Vulnerability Assessment + Penetration Testing |

## 3.2 Out-of-Scope

The following activities were outside the scope of this assessment:

* Production systems
* Internet-facing infrastructure
* Third-party systems
* Social engineering
* Physical security testing
* Denial-of-service testing
* Destructive exploitation
* Persistence mechanisms

---

# 4. Assessment Methodology

The assessment followed a structured VAPT methodology consisting of the following phases:

```text
┌─────────────────────────┐
│ 1. Reconnaissance       │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ 2. Enumeration          │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ 3. Vulnerability        │
│    Assessment           │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ 4. Exploitation         │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ 5. Post-Exploitation    │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ 6. Risk Assessment      │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ 7. Remediation          │
│    Recommendations      │
└────────────┬────────────┘
             ↓
┌─────────────────────────┐
│ 8. Retesting            │
└─────────────────────────┘
```

---

# 5. Tools Used

| Tool                 | Purpose                      |
| -------------------- | ---------------------------- |
| Kali Linux           | Penetration-testing platform |
| Nmap                 | Host and service discovery   |
| Metasploit Framework | Exploit validation           |
| Meterpreter          | Post-exploitation validation |
| Wireshark            | Network traffic analysis     |

---

# 6. Reconnaissance

## 6.1 Target Identification

The assessment began by establishing connectivity between the Kali Linux assessment system and the Metasploitable 2 target within the isolated host-only network.

### Assessment Infrastructure

```text
Attacker
Kali Linux
192.168.15.137
       │
       │ Host-Only Network
       │
       ▼
Target
Metasploitable 2
192.168.15.129
```

Basic connectivity was verified before service enumeration was performed.

### Evidence

* `01_metasploitable_ip.png`
* `02_target_ping.png`
* `03_Attacker_ip.png`

---

# 7. Network & Service Enumeration

Nmap was used to identify exposed TCP services and determine the software versions associated with those services.

The assessment identified several accessible services.

|     Port | Service    | Identified Software / Observation |
| -------: | ---------- | --------------------------------- |
|   21/tcp | FTP        | vsftpd 2.3.4                      |
|   22/tcp | SSH        | Open                              |
|   23/tcp | Telnet     | Open                              |
|   25/tcp | SMTP       | Open                              |
|   80/tcp | HTTP       | Open                              |
|  139/tcp | SMB        | Open                              |
|  445/tcp | SMB        | Open                              |
| 3306/tcp | MySQL      | Open                              |
| 5432/tcp | PostgreSQL | Open                              |

The most significant discovery during enumeration was the FTP service running **vsftpd 2.3.4**.

This version was investigated further during vulnerability assessment.

### Evidence

* `04_nmap-sv_scan.png`
* `05_nmap_21_ftp_scan.png`

---

# 8. Vulnerability Assessment

## Finding VAPT-001: VSFTPD 2.3.4 Backdoor

### Finding Overview

| Attribute        | Details                    |
| ---------------- | -------------------------- |
| Finding ID       | VAPT-001                   |
| Vulnerability    | VSFTPD 2.3.4 Backdoor      |
| CVE              | CVE-2011-2523              |
| Affected Service | FTP                        |
| Port             | 21/tcp                     |
| Severity         | **Critical**               |
| Exploitability   | High                       |
| Impact           | Remote unauthorized access |
| Validation       | Successfully Exploited     |

### Description

The target system was found to be running **vsftpd 2.3.4**, a version associated with a known backdoor vulnerability identified as **CVE-2011-2523**.

The vulnerable service exposes an attack surface through FTP. Successful exploitation can provide unauthorized shell access to the affected system.

During this assessment, exploitation was successfully validated, resulting in root-level access.

---

# 9. Exploitation

## 9.1 Exploit Identification

The Metasploit Framework was used to identify an appropriate exploit module for the discovered vulnerable service.

The following module was selected:

```text
exploit/unix/ftp/vsftpd_234_backdoor
```

The target was configured as:

```text
RHOSTS = 192.168.15.129
```

The assessment system used:

```text
LHOST = 192.168.15.137
```

---

## 9.2 Exploitation Result

The exploit was executed against the vulnerable FTP service.

The exploitation was successful and resulted in a remote session.

```text
Vulnerable FTP Service
        ↓
CVE-2011-2523
        ↓
Metasploit Exploitation
        ↓
Remote Session
        ↓
Root-Level Access
```

### Assessment Result

**VULNERABLE — EXPLOITATION SUCCESSFUL**

The successful exploitation confirms that the vulnerability was not merely theoretical and could be used to obtain unauthorized access to the target system.

### Evidence

* `06_msf_startup.png`
* `07_msfconsole_search_vsftpd.png`
* `08_use_vsftpd.png`
* `09_show_options.png`
* `10_set_rhosts.png`
* `11_set_lhosts.png`
* `12_show_options2.png`
* `13_exploit_sucess.png`

---

# 10. Post-Exploitation Assessment

After successful exploitation, limited post-exploitation activities were performed to determine the level of access obtained.

The purpose was to validate the impact of the vulnerability rather than maintain persistence or perform destructive actions.

## 10.1 Session Verification

The established Meterpreter session was inspected to confirm successful access to the target.

## 10.2 Privilege Verification

The effective user context was checked.

Root-level privileges were confirmed.

This significantly increases the severity of the vulnerability because an attacker would not be restricted to a low-privileged account.

## 10.3 System Information

System information was collected to verify the compromised operating system and environment.

## 10.4 Network Configuration

The target's network configuration was inspected to confirm the compromised host's network identity.

## 10.5 Filesystem Enumeration

Basic filesystem enumeration was performed to validate the level of access available to the compromised session.

## 10.6 User Enumeration

The `/etc/passwd` file was examined as part of controlled post-exploitation validation.

### Evidence

* `14_sessions.png`
* `15_getuid-sysinfo.png`
* `16_shell.png`
* `17_ip_addr.png`
* `18_ls_listing.png`
* `19_password.png`

---

# 11. Network Traffic Analysis

Wireshark was used to analyze network traffic generated during the assessment.

Traffic between the Kali Linux assessment system and Metasploitable 2 was observed during the exploitation process.

Relevant hosts included:

```text
Attacker: 192.168.15.137
Target:   192.168.15.129
```

The packet capture provides supporting network-level evidence of communication associated with the exploitation activity.

### Evidence

* `20_wireshark.png`

> Wireshark was used as supporting technical evidence during the penetration test rather than as an incident-response detection system.

---

# 12. Attack Path

The complete attack path demonstrated during the assessment was:

```text
Target Discovery
       ↓
Port Scanning
       ↓
Service Enumeration
       ↓
FTP / vsftpd 2.3.4 Identified
       ↓
Vulnerability Identification
       ↓
CVE-2011-2523
       ↓
Exploit Selection
       ↓
Metasploit Exploitation
       ↓
Remote Session
       ↓
Root Privileges
       ↓
Full Host Compromise
```

The attack path demonstrates how an exposed vulnerable service can provide a direct path from network reconnaissance to complete system compromise.

---

# 13. Risk Assessment

## 13.1 Severity: CRITICAL

The vulnerability was classified as **Critical** because successful exploitation resulted in root-level remote access.

### Impact Analysis

| Security Property | Impact   | Explanation                                          |
| ----------------- | -------- | ---------------------------------------------------- |
| Confidentiality   | Critical | Files and system information could be accessed       |
| Integrity         | Critical | Root access permits unauthorized modification        |
| Availability      | High     | Privileged access could allow service disruption     |
| Privilege         | Critical | Root-level privileges obtained                       |
| Exploitability    | High     | Known vulnerability with public exploitation tooling |

---

# 14. Potential Business Impact

If the same vulnerability existed on a production server, successful exploitation could potentially result in:

* Unauthorized system access
* Sensitive information disclosure
* Unauthorized modification of files
* Credential exposure
* Malware installation
* Lateral movement
* Privilege abuse
* Service disruption
* Complete host takeover

The actual business impact would depend on the role of the affected system and the sensitivity of the data accessible from it.

---

# 15. Remediation Recommendations

## VAPT-001 — VSFTPD 2.3.4 Backdoor

### Priority: Critical

The vulnerable FTP service should be addressed immediately.

### Recommended Actions

**1. Remove the vulnerable service if FTP is unnecessary**

If FTP functionality is not required, disable and remove the service.

**2. Upgrade to a supported and secure version**

If file-transfer functionality is required, replace the vulnerable software with a supported version that is not affected by the identified vulnerability.

**3. Prefer secure file-transfer protocols**

Where applicable, use:

* SFTP
* SCP
* HTTPS-based file transfer

instead of legacy FTP.

**4. Restrict network access**

FTP should not be exposed to untrusted networks.

Implement:

* Firewall rules
* Access control lists
* Network segmentation
* Host-based firewall policies

**5. Implement vulnerability management**

Maintain an ongoing process for:

```text
Asset Discovery
      ↓
Vulnerability Scanning
      ↓
Risk Prioritization
      ↓
Patch Management
      ↓
Validation
      ↓
Periodic Retesting
```

**6. Minimize exposed services**

Disable unnecessary legacy services such as Telnet and other unused network services.

**7. Apply least privilege**

Network-facing services should operate with the minimum privileges required to perform their intended function.

---

# 16. Retesting Methodology

After remediation, the affected system should be retested to determine whether the vulnerability has been successfully addressed.

### Recommended Retest

```text
Remediation Applied
        ↓
Nmap Service Scan
        ↓
Verify FTP Service
        ↓
Verify Software Version
        ↓
Controlled Vulnerability Validation
        ↓
Confirm Exploitation No Longer Possible
        ↓
Finding Status Updated
```

### Expected Retest Result

The vulnerable **vsftpd 2.3.4** service should no longer be exposed or exploitable.

### Finding Status

**Initial Assessment:** Vulnerable / Exploitable

**Post-Remediation:** To be verified through retesting

> The Metasploitable 2 VM was restored to a clean snapshot after the lab assessment to return the environment to its original state.

---

# 17. Findings Summary

| Finding ID | Vulnerability                         | Severity     | Exploitation | Recommended Priority |
| ---------- | ------------------------------------- | ------------ | ------------ | -------------------- |
| VAPT-001   | VSFTPD 2.3.4 Backdoor — CVE-2011-2523 | **Critical** | Successful   | Immediate            |

### Assessment Statistics

| Metric                 | Result |
| ---------------------- | -----: |
| Hosts Assessed         |      1 |
| Critical Findings      |      1 |
| High Findings          |      0 |
| Medium Findings        |      0 |
| Low Findings           |      0 |
| Successfully Exploited |      1 |
| Root Access Obtained   |    Yes |

---

# 18. MITRE ATT&CK Mapping

The penetration-testing activities can be mapped to relevant MITRE ATT&CK techniques.

| Activity                                  | ATT&CK Technique                          | ID     |
| ----------------------------------------- | ----------------------------------------- | ------ |
| Network Service Scanning                  | Network Service Scanning                  | T1046  |
| Exploitation of Public-Facing Application | Exploitation of Public-Facing Application | T1190  |
| Command Execution                         | Command and Scripting Interpreter         | T1059  |
| Privilege Escalation / Privileged Access  | Privilege Escalation                      | TA0004 |
| File and Directory Discovery              | File and Directory Discovery              | T1083  |
| Account Discovery                         | Account Discovery                         | T1087  |

> ATT&CK mappings are provided as contextual references for the demonstrated penetration-testing activities.

---

# 19. Evidence & Screenshots

## 19.1 Reconnaissance & Enumeration

### 01 — Metasploitable 2 IP Configuration

![Metasploitable 2 IP Configuration](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/01_metasploitable_ip.png)

### 02 — Target Connectivity Test

![Target Connectivity Test](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/02_target_ping.png)

### 03 — Kali Linux IP Configuration

![Kali Linux IP Configuration](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/03_Attacker_ip.png)

### 04 — Nmap Service and Version Scan

![Nmap Service and Version Scan](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/04_nmap-sv_scan.png)

### 05 — FTP Service Enumeration

![FTP Service Enumeration](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/05_nmap_21_ftp_scan.png)


## 19.2 Vulnerability Identification & Exploitation

### 06 — Metasploit Framework Startup

![Metasploit Framework Startup](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/06_msf_startup.png)

### 07 — VSFTPD Exploit Search

![VSFTPD Exploit Search](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/07_msfconsole_search_vsftpd.png)

### 08 — VSFTPD Exploit Module Selection

![VSFTPD Exploit Module Selection](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/08_use_vsftpd.png)

### 09 — Exploit Configuration Options

![Exploit Configuration Options](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/09_show_options.png)

### 10 — RHOSTS Configuration

![RHOSTS Configuration](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/10_set_rhosts.png)

### 11 — LHOST Configuration

![LHOST Configuration](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/11_set_lhosts.png)

### 12 — Final Exploit Configuration

![Final Exploit Configuration](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/12_show_options2.png)

### 13 — Successful Exploitation

![Successful Exploitation](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/13_exploit_sucess.png)


## 19.3 Post-Exploitation & Privilege Verification

### 14 — Meterpreter Session

![Meterpreter Session](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/14_sessions.png)

### 15 — System and Privilege Verification

![System and Privilege Verification](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/15_getuid-sysinfo.png)

### 16 — Root Shell Access

![Root Shell Access](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/16_shell.png)

### 17 — Network Configuration

![Network Configuration](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/17_ip_addr.png)

### 18 — Filesystem Enumeration

![Filesystem Enumeration](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/18_ls_listing.png)

### 19 — User Account Enumeration

![User Account Enumeration](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/19_password.png)


## 19.4 Network Traffic Analysis

### 20 — Wireshark Traffic Capture

![Wireshark Traffic Capture](https://raw.githubusercontent.com/imamhussain17/Incident-response/main/20_wireshark.png)

# 20. Conclusion

The VAPT assessment identified a critical vulnerability in the FTP service running on the Metasploitable 2 target.

The assessment progressed through reconnaissance, service enumeration, vulnerability identification, controlled exploitation, and post-exploitation validation.

The **VSFTPD 2.3.4 Backdoor vulnerability (CVE-2011-2523)** was successfully exploited using the Metasploit Framework, resulting in root-level remote access.

This demonstrates that the identified vulnerability could provide an attacker with a direct path to complete host compromise.

The primary remediation measures are to remove or upgrade the vulnerable FTP service, restrict unnecessary network exposure, implement secure file-transfer mechanisms, maintain effective patch management, and conduct periodic vulnerability assessments and penetration tests.

This project demonstrates practical experience in:

* Network reconnaissance
* Port and service enumeration
* Vulnerability identification
* CVE research
* Exploit validation
* Metasploit Framework
* Meterpreter
* Privilege verification
* Post-exploitation analysis
* Network traffic analysis
* Vulnerability risk assessment
* Remediation planning
* Retesting methodology
* Professional security reporting

---

# 21. Disclaimer

This assessment was conducted solely within an isolated home-lab environment using an intentionally vulnerable Metasploitable 2 virtual machine.

No unauthorized production systems, third-party infrastructure, personal systems, or external networks were targeted.

This project is intended exclusively for cybersecurity education, practical skill development, and professional portfolio demonstration.
