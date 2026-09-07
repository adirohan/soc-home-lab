# 06 - Windows Security Monitoring

In this lab section, I enabled advanced audit policies on the Windows 11 endpoint (`SOC-WIN01`) to log critical security events like user creation, privilege changes, and process execution, and forwarded them to the Wazuh SIEM manager.

## Setup & Audit Policy Configuration

By default, Windows doesn't capture detailed process creation or account changes in its logs. I configured granular audit policies using the Command Prompt (as Administrator):

```cmd
auditpol /set /category:"Logon/Logoff" /success:enable /failure:enable
auditpol /set /category:"Account Management" /success:enable /failure:enable
auditpol /set /subcategory:"Process Creation" /success:enable /failure:enable
