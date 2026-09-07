# 06 - Windows Security Monitoring

In this lab section, I enabled advanced audit policies on the Windows 11 endpoint (`SOC-WIN01`) to log critical security events like user creation, privilege changes, and process execution, and forwarded them to the Wazuh SIEM manager.

## Setup & Audit Policy Configuration

By default, Windows doesn't capture detailed process creation or account changes in its logs. I configured granular audit policies using the Command Prompt (as Administrator):

auditpol /set /category:"Logon/Logoff" /success:enable /failure:enable
auditpol /set /category:"Account Management" /success:enable /failure:enable
auditpol /set /subcategory:"Process Creation" /success:enable /failure:enable

To make sure full command-line arguments are recorded whenever a process starts (Event ID 4688), I updated the registry key:

reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\Audit" /v ProcessCreationIncludeCmdLine_Enabled /t REG_DWORD /d 1 /f

---

## Controlled Activity & Detection

### 1. User Creation Test
* **Command Executed:**
  net user TestSOCUser Password123! /add

* **Observation in Wazuh:**
  Wazuh picked up the Windows Security event immediately. Two main alert rules were triggered:
  - `rule.id: 60109` (*User account enabled or created*) — Level 8 Severity
  - `rule.id: 60110` (*User account changed*) — Level 8 Severity

### 2. Process Execution Tracking
* **Observation in Wazuh:**
  With command-line logging enabled, Wazuh streamed process creation events (`rule.id: 60727`), allowing me to view exact binary paths and executed command strings from `SOC-WIN01`.

---

## Screenshots & Proof

| Task | File Path |
| :--- | :--- |
| **Audit Policy & CLI Commands** | `screenshots/01-windows-audit-setup.png` |
| **Wazuh Account Creation Alerts** | `screenshots/02-wazuh-event-detection.png` |
