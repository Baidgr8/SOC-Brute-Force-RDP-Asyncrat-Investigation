# Brute Force to RDP Compromise: SIEM & Windows Log Investigation of AsyncRAT C2 Activity

**Windows Security Logs • Sysmon • SIEM Investigation • MITRE ATT&CK**

**Incident Type:** Brute Force → Credential Compromise → Persistence → Privilege Escalation → Command & Control  
**Severity:** High  
**Prepared By:** Babafemi A. Ikujebi  
**Role:** SOC Analyst (Simulated SOC Environment – TryHackMe)  
**Date of Investigation:** 19 February 2026  
**Environment:** Windows Server – TryHackMe SOC Simulation  

---

# Recruiter Quick Overview

This project demonstrates a **SOC investigation of a multi-stage attack detected through SIEM authentication alerts**.

The investigation identified:

- Brute force attempts targeting a privileged Windows account
- Successful credential compromise
- Unauthorized RDP access
- Persistence through malicious account creation
- Privilege escalation via group membership manipulation
- Execution of AsyncRAT malware
- Active command-and-control communication

The investigation confirms a **complete attack lifecycle and full host compromise**.
## Incident Dashboard – Authentication Compromise Overview


#### 1. Authentication Activity Summary

**Time Window:** 10:53:26 PM – 10:53:41 PM  
**Target Account:** Administrator  
**Source IP:** 10.10.53.248  

| Metric | Value |
|------|------|
| Failed Logins (Event ID 4625) | 14 |
| Successful Logins (Event ID 4624) | 2 |
| Logon Type 3 (Network Logon) | 1 |
| Logon Type 10 (RDP Logon) | 1 |
| Time to Credential Compromise | 4 seconds |
| Time to Interactive Access | 11 seconds |



### 2. Post-Compromise Activity

| Activity | Event ID | Impact |
|--------|--------|--------|
| Account Creation | 4720 | Persistence |
| Group Membership Change | 4732 | Privilege Escalation |
| Process Execution | Sysmon 1 | Malware Execution |
| DNS Query | Sysmon 22 | External Beacon |
| Network Connection | Sysmon 3 | Active Command & Control |



### 3. Malicious Artifacts Identified

| Indicator Type | Value |
|---------------|------|
| Malware | AsyncRAT |
| File Name | ckjg.exe |
| SHA256 | 08037DE4A729634FA818DDF03DDD27C28C89F42158AF5EDE71CF0AE2D78FA198 |
| Malicious Domain | hkfasfsafg.click |
| Malicious IP | 193.46.217.4 |
| C2 Port | TCP 7777 |



### 4. Attack Lifecycle


`Credential Access` → `Initial Access` → `Persistence` → `Privilege Escalation` → `Malware Execution` → `Command & Control`


---

# SOC Case Snapshot

| Field | Details |
|------|------|
| Alert Source | SIEM Authentication Alert |
| Detection Method | Excessive Failed Login Threshold |
| Affected Asset | Windows Server |
| Targeted Account | Administrator |
| Source IP | 10.10.53.248 |
| Malware | AsyncRAT |
| C2 Infrastructure | hkfasfsafg.click |
| Command & Control Port | TCP 7777 |
| Status | Confirmed Compromise |

---

# Incident Overview


On **19 February 2026**, the SIEM generated an alert indicating **abnormal authentication activity targeting a Windows Server**.

The alert was triggered after detecting **multiple failed login attempts against the Administrator account** within a short timeframe.

The investigation aimed to determine whether the activity resulted in **successful credential compromise or unauthorized system access**.

**Initial Classification:** Suspected Brute Force Attempt  
**Final Classification:** Confirmed Credential Compromise with Active Command & Control Communication

---

# Executive Summary

Analysis of Windows Security Event Logs identified **14 failed login attempts (Event ID 4625)** targeting the **Administrator account** within a **4-second window**.

**Time Range:**  
10:53:26 PM – 10:53:30 PM

**Source IP Address:**  
10.10.53.248

The failure **sub-status code `0xC000006A`** indicates **incorrect password attempts against a valid username**, consistent with **credential guessing behavior**.

Immediately following these failed attempts:

- A **successful authentication event (Event ID 4624 – Logon Type 3)** was recorded.
- **11 seconds later**, another **Event ID 4624 (Logon Type 10)** confirmed successful **Remote Desktop (RDP) access** using the compromised Administrator account.

Following confirmation of unauthorized access, the investigation expanded to analyze **post-compromise attacker activity**.

---

# Attack Flow Analysis

The investigation confirmed the following attack progression:

<div align="center">

`Brute Force Attack`
<br>
↓
<br>
`Credential Compromise`
<br>
↓
<br>
`Remote Desktop Access`
<br>
↓
<br>
`Persistence via Backdoor Account`
<br>
↓
<br>
`Privilege Escalation`
<br>
↓
<br>
`Malware Execution`
<br>
↓
<br>
`Command & Control Communication`
</div>

---

# MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|------|------|------|
| Credential Access | Brute Force | T1110 |
| Initial Access | Valid Accounts | T1078 |
| Lateral Movement | Remote Services (RDP) | T1021.001 |
| Persistence | Create Account | T1136 |
| Privilege Escalation | Account Manipulation | T1098 |
| Execution | Command Execution | T1059 |
| Command & Control | Application Layer Protocol | T1071 |

---

# Indicators of Compromise (IOCs)

| Indicator Type | Value |
|------|------|
| Source IP | 10.10.53.248 |
| Compromised Account | Administrator |
| Malicious Account | svc_sysrestore |
| Malware File | ckjg.exe |
| SHA256 | 08037DE4A729634FA818DDF03DDD27C28C89F42158AF5EDE71CF0AE2D78FA198 |
| Malicious Domain | hkfasfsafg.click |
| Malicious IP | 193.46.217.4 |
| C2 Port | TCP 7777 |

---

# Investigation Timeline

| Time (UTC) | Event | Evidence | Conclusion |
|------|------|------|------|
| 10:53:26 PM | Multiple failed logins | Event ID 4625 | Brute force detected |
| 10:53:30 PM | Successful login | Event ID 4624 (Type 3) | Credentials validated |
| 10:53:41 PM | RDP session established | Event ID 4624 (Type 10) | Unauthorized access |
| 10:54:58 PM | Account created | Event ID 4720 | Persistence |
| 10:55:12 PM | Added to Backup Operators | Event ID 4732 | Privilege escalation |
| 10:55:24 PM | Added to Remote Desktop Users | Event ID 4732 | Persistent RDP access |
| 4:08:43 PM | DNS + outbound traffic | Sysmon Event ID 22 & 3 | Active C2 |

---

# Technical Investigation

### Authentication Failure Analysis

Windows Security logs revealed **14 failed login attempts within 4 seconds** targeting the **Administrator account**.

Event **ID 4625** showed the failure **sub-status code `0xC000006A`**, indicating **incorrect password attempts against a valid account**.

This pattern is consistent with **automated brute-force activity**.

 Event ID 4625 Brute Force Attempts
![Process Tree Analysis](https://github.com/Baidgr8/SOC-Brute-Force-RDP-Asyncrat-Investigation/blob/2fbcbd0496f4a8b8c09273cab6061f61a1434130/assets/event%20log%20showing%20account%20name%20Administration.PNG)

---

## Credential Compromise Confirmation

A successful **Event ID 4624 (Logon Type 3)** occurred immediately after the failed attempts from the same IP address.

Within **11 seconds**, a second **Event ID 4624 (Logon Type 10)** confirmed a successful **Remote Desktop login**.

The **IP address correlation and timing** confirm successful credential compromise.

Successful Login Event
![Process Tree Analysis](https://github.com/Baidgr8/SOC-Brute-Force-RDP-Asyncrat-Investigation/blob/b536a8cfebdd01b44a4e79678229074016e37e2c/assets/succesfull%20login%20on%20network.PNG)
screenshots event id 4624-login type 3

![Process Tree Analysis](https://github.com/Baidgr8/SOC-Brute-Force-RDP-Asyncrat-Investigation/blob/195685772d38d502813281b8bc3e1d7b11024a6b/assets/Change%20to%20RDP%20succesful%20login.PNG)
Successful Remote Desktop login login type 10


---

## Persistence Establishment

Event **ID 4720** confirmed the creation of a new account:

![Process Tree Analysis](https://github.com/Baidgr8/SOC-Brute-Force-RDP-Asyncrat-Investigation/blob/f77f704c1bdee1bb4d98b68185a7e1e3a1a5b534/assets/account%20creation%20ID%204789.PNG)
the account named svc_sysrestore was created


This account was created under the compromised **Administrator session**.

The naming convention suggests an attempt to **blend with legitimate system service accounts**.


---

## Privilege Escalation

Event **ID 4732** logs show the new account was rapidly added to multiple privileged groups:

- Users
- Backup Operators
- Remote Desktop Users

This allowed the attacker to **maintain privileged access and persistent RDP connectivity**.

**Privileged Group Membership**

![Process Tree Analysis](https://github.com/Baidgr8/SOC-Brute-Force-RDP-Asyncrat-Investigation/blob/5af8a4a9df101c45fc1ad84bfb1fc2901dee1585/assets/member%20added%204732.PNG)
screenshots event id 4732 groupmembership


---

## Malware Execution

Sysmon telemetry confirmed execution of a malicious file:

```
File Name: ckjg.exe
Path: C:\Users\sarah.miller\Downloads\ckjg.exe
SHA256: 08037DE4A729634FA818DDF03DDD27C28C89F42158AF5EDE71CF0AE2D78FA198
Detection Score: 60/72 (VirusTotal)
Malware Family: AsyncRAT

```

Threat intelligence analysis using **VirusTotal** detected the file as **AsyncRAT malware** with a **60/72 detection score**.

**VirusTotal Detection Results**
![Process Tree Analysis](https://github.com/Baidgr8/SOC-Brute-Force-RDP-Asyncrat-Investigation/blob/5f48f02ecf947161c02386cc9fca9632b3c59da1/assets/virus%20total%20scan.PNG)

screenshots of virustotal-analysis


---

## Command & Control Communication

Sysmon logs revealed outbound communication to malicious infrastructure.

| Indicator | Value |
|------|------|
| Domain | hkfasfsafg.click |
| IP Address | 193.46.217.4 |
| Port | TCP 7777 |

The use of **non-standard TCP port 7777** is commonly associated with **Remote Access Trojan command channels**.

**Sysmon DNS & Network Connection Logs**
![Process Tree Analysis](https://github.com/Baidgr8/SOC-Brute-Force-RDP-Asyncrat-Investigation/blob/1f76180503d26fbdd0033d6fb5b1963cad1f3c3f/assets/sysmon%20id%201.PNG)
screenshots/sysmon-c2-traffic.png

---

# Detection Opportunities

Security monitoring tools could detect this attack using:

- Excessive **Event ID 4625 failed login alerts**
- Correlation between **failed and successful login events**
- Monitoring **Event ID 4720 account creation**
- Alerting on **privileged group membership changes**
- Detection of **outbound traffic on unusual ports**
- DNS monitoring for **malicious domains**

---

# Containment & Remediation

### Immediate Actions

- Disable compromised **Administrator account**
- Remove **svc_sysrestore persistence account**
- Isolate affected host
- Block malicious **domain and IP address**

---

### Medium-Term Improvements

- Implement **account lockout policies**
- Enforce **multi-factor authentication (MFA)**
- Restrict **RDP access behind VPN**

---

### Long-Term Security Improvements

- Deploy centralized **SIEM monitoring**
- Implement **Privileged Access Management (PAM)**
- Conduct enterprise-wide **credential hygiene audit**

---

# Skills Demonstrated

- SIEM investigation and alert triage
- Windows Security Event Log analysis
- Sysmon log analysis
- Threat intelligence correlation
- Malware analysis
- MITRE ATT&CK mapping
- SOC incident reporting
