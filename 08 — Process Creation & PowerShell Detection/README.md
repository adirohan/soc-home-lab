# 08. Windows Process Creation & PowerShell Detection

This section demonstrates Windows process creation monitoring and PowerShell activity detection using Windows Security Event ID 4688 and Wazuh.

The objective was to enable process creation auditing, generate controlled process activity, and verify that the resulting telemetry was collected and visible through Wazuh.

---

## 08.1 Process Creation Auditing

Windows Process Creation auditing was enabled using an elevated PowerShell session.

The following audit policy was configured:

    auditpol /set /subcategory:"Process Creation" /success:enable /failure:enable

This enables Windows to generate Security Event ID 4688 whenever a new process is created.

### Evidence

![Windows Process Creation Audit](screenshots/01-process-creation-4688.png)

---

## 08.2 Windows Event ID 4688 — Process Creation

A controlled process was executed on the Windows endpoint to generate Event ID 4688.

The event confirms that Windows recorded the creation of a new process.

### Evidence

![Windows Event 4688 Process Creation Details](screenshots/02-process-creation-4688-details.png)

---

## 08.3 Event 4688 Command-Line Information

The generated Event ID 4688 was inspected to verify the command-line information associated with the created process.

Command-line visibility is valuable for SOC investigations because it provides additional context about how a process was executed.

### Evidence

![Event 4688 Command Line Details](screenshots/02-4688-command-line-process-details.png)

---

## 08.4 Wazuh Process Creation Detection

The Windows process creation event was collected by the Wazuh agent and displayed in the Wazuh dashboard.

The event included process-related information such as:

- Agent name
- Process ID
- New process name
- Parent process
- Command line
- User information

### Evidence

![Wazuh Event 4688 Process Investigation](screenshots/03-wazuh-4688-process-investigation.png)

---

## 08.5 PowerShell Process Creation

PowerShell activity was generated on the Windows endpoint to demonstrate how PowerShell execution appears in Windows Event ID 4688.

The command-line information provides useful context for identifying PowerShell-based activity.

### Evidence

![Event 4688 PowerShell Command Line](screenshots/04-4688-powershell-command-line.png)

---

## 08.6 Wazuh PowerShell Process Detection

The PowerShell process creation event was successfully collected and displayed by Wazuh.

This provides visibility into PowerShell execution occurring on the monitored Windows endpoint.

### Evidence

![Wazuh PowerShell Process Detection](screenshots/04-wazuh-powershell-process-detection.png)

---

## 08.7 Encoded PowerShell Activity

A controlled PowerShell command using the `-EncodedCommand` parameter was executed.

Encoded PowerShell commands are important from a SOC perspective because attackers may use encoding to make command-line activity less immediately readable.

### Evidence

![Wazuh Encoded PowerShell](screenshots/05-wazuh-encoded-powershell.png)

---

## 08.8 PowerShell Command-Line Visibility in Wazuh

The Wazuh event was inspected to verify that PowerShell command-line information was available for investigation.

This demonstrates how command-line telemetry can help a SOC analyst understand process execution.

### Evidence

![Wazuh PowerShell Command](screenshots/06-wazuh-powershell-command.png)

---

## 08.9 Encoded PowerShell Detection

The encoded PowerShell process was further investigated through the Wazuh event data.

The event contained the PowerShell executable and encoded command information in the process command-line field.

### Evidence

![Wazuh Encoded PowerShell Detection](screenshots/07-wazuh-encoded-powershell-detection.png)

---

## 08.10 PowerShell Command Decoding

The encoded PowerShell command was examined to demonstrate how encoded command-line activity can be investigated by a SOC analyst.

### Evidence

![PowerShell Command Decoded](screenshots/08-powershell-command-decoded.png)

---

## 08.11 Detection Workflow

The practical followed this workflow:

Windows Process Creation Audit
→ Controlled Process Execution
→ Security Event ID 4688
→ Wazuh Agent Collection
→ Wazuh Detection
→ SOC Investigation

---

## 08.12 SOC Analyst Takeaway

Windows Event ID 4688 provides visibility into process creation on Windows endpoints.

For a SOC analyst, process creation telemetry can help identify:

- Suspicious PowerShell execution
- Unusual parent-child process relationships
- Encoded commands
- Unexpected executables
- Suspicious command-line activity
- Potential execution of attacker tools

The presence of a PowerShell or encoded-command event alone does not prove malicious activity. The analyst should investigate the process, user, parent process, command line, timing, and surrounding events before determining whether the activity is suspicious.

---

## 08.13 Practical Result

This practical successfully demonstrated:

- Windows Process Creation auditing
- Security Event ID 4688
- PowerShell process monitoring
- Command-line visibility
- Encoded PowerShell activity
- Wazuh collection of Windows process telemetry
- SOC-style process investigation
