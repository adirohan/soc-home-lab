# Process Creation & PowerShell Detection — Wazuh SOC Lab

## Overview

This lab demonstrates how Windows process creation and PowerShell activity can be monitored and investigated using Wazuh in a SOC environment.

The objective is to understand how a SOC analyst can detect suspicious process activity, investigate the generated security events, identify parent-child process relationships, and analyze encoded or suspicious PowerShell commands.

The lab focuses on Windows process creation events and their investigation through the Wazuh dashboard.

---

## Lab Environment

| Component | Details |
|---|---|
| SIEM / SOC Platform | Wazuh |
| Endpoint | Windows |
| Detection Source | Windows Security / Sysmon Events |
| Activity | Process Creation |
| Primary Focus | PowerShell Detection |
| Investigation Platform | Wazuh Dashboard |

---

## Objectives

- Understand Windows process creation events.
- Enable and verify process creation auditing.
- Generate process creation events on the Windows endpoint.
- Detect process creation activity using Wazuh.
- Investigate Windows Event ID 4688.
- Understand parent-child process relationships.
- Investigate PowerShell command-line activity.
- Identify encoded PowerShell commands.
- Analyze suspicious PowerShell execution.
- Understand how process creation information supports SOC investigations.

---

## 1. Process Creation and Windows Event ID 4688

Windows generates security events when new processes are created.

One of the important events for process monitoring is:

**Event ID 4688 — A new process has been created**

This event can provide useful information such as:

- New process name
- Process ID
- Parent process
- Parent process ID
- Command-line information
- User/account involved
- Process creation time

For a SOC analyst, this information helps determine whether a process was expected or potentially suspicious.

---

## 2. Enabling Process Creation Auditing

Process Creation auditing was enabled on the Windows endpoint so that Windows could generate security events whenever a new process was created.

This provides visibility into process execution and allows Wazuh to collect and analyze these events.

The basic flow is:

Windows Endpoint
↓
Process Created
↓
Windows Security Event
↓
Wazuh Agent
↓
Wazuh Manager
↓
Wazuh Dashboard
↓
SOC Investigation

---

## 3. Generating a Process Creation Event

After process creation auditing was enabled, a new process was manually executed on the Windows endpoint.

The resulting Windows Security event was collected by the Wazuh agent.

This demonstrates the complete path from an activity occurring on the endpoint to the event becoming available for investigation inside the SOC platform.

---

## 4. Investigating Event ID 4688

The generated process creation event was investigated in the Wazuh dashboard.

Event ID 4688 is particularly useful because it records the creation of a new process.

During investigation, the following information can be examined:

- Event ID
- Process name
- Process ID
- Parent process
- Parent process ID
- Command line
- User
- Timestamp
- Host information

A SOC analyst can use these fields to reconstruct what happened on the endpoint.

---

## 5. Parent and Child Processes

Processes commonly create other processes.

The process that launches another process is called the **parent process**.

The newly created process is called the **child process**.

Example:

explorer.exe
↓
powershell.exe
↓
cmd.exe

Understanding this relationship is important during incident investigation.

A suspicious child process may become much more interesting when its parent process is unexpected.

---

## 6. PowerShell Command-Line Activity

PowerShell is a legitimate Windows administration tool, but it is also frequently used by attackers because it provides extensive capabilities for executing commands and scripts.

For this reason, PowerShell activity is important for SOC monitoring.

Useful investigation fields include:

- PowerShell process name
- Parent process
- Command-line arguments
- User account
- Process creation time
- Encoded commands
- Child processes created by PowerShell

PowerShell should not automatically be considered malicious. The analyst should investigate the context surrounding its execution.

---

## 7. Generating PowerShell Activity

PowerShell commands were executed on the Windows endpoint to generate process creation telemetry.

The resulting events were collected by Wazuh and investigated through the dashboard.

This demonstrates how normal endpoint activity can be converted into security telemetry that can later be analyzed by a SOC analyst.

---

## 8. Encoded PowerShell Detection

Encoded PowerShell commands are commonly encountered during security investigations.

PowerShell can execute commands using encoded input, which can make the original command less immediately visible during an investigation.

A SOC analyst should pay attention to PowerShell command-line arguments containing indicators such as:

- `-EncodedCommand`
- `-enc`

The presence of an encoded command does not by itself prove malicious activity, but it is an important investigation indicator.

---

## 9. Investigating Encoded PowerShell

The encoded PowerShell activity was investigated using the information available in the Wazuh event.

The investigation focused on:

- Identifying the PowerShell process.
- Examining the command line.
- Identifying encoded PowerShell parameters.
- Investigating the parent process.
- Examining the process creation event.
- Reviewing the available event metadata.
- Understanding how the activity appeared in Wazuh.

The goal is not simply to find an alert, but to understand the complete execution chain.

---

## 10. Decoding PowerShell Commands

When an encoded PowerShell command is discovered, the SOC analyst can decode it to understand what was actually executed.

A typical investigation flow is:

PowerShell Event
↓
Identify Encoded Command
↓
Extract Encoded Data
↓
Decode Command
↓
Understand Command Behaviour
↓
Determine Whether Activity Is Suspicious

Decoding provides additional context that may not be immediately visible from the original event.

---

## 11. PowerShell Detection with Command-Line Details

Command-line information provides valuable context during endpoint investigations.

For PowerShell-related events, the analyst can examine:

- Executable name
- Arguments
- Encoded parameters
- Parent process
- Child process
- User
- Host
- Timestamp

Combining these fields allows the analyst to build a clearer picture of what occurred on the endpoint.

---

## Investigation Flow

The complete investigation performed in this lab can be summarized as:

Windows Endpoint
↓
Process Created
↓
Event ID 4688 Generated
↓
Wazuh Agent Collects Event
↓
Wazuh Manager Processes Event
↓
Event Appears in Wazuh Dashboard
↓
SOC Analyst Investigates
↓
Parent / Child Process Analysis
↓
PowerShell Command-Line Analysis
↓
Encoded PowerShell Investigation
↓
Command Decoding
↓
Determine Activity Context

---

## Key SOC Concepts Learned

### Event ID 4688 — Process Creation

Windows Event ID 4688 records the creation of a new process and provides useful information for endpoint investigations.

### Parent-Child Process Relationship

Understanding which process launched another process helps analysts identify unusual execution chains.

### PowerShell Monitoring

PowerShell activity should be monitored because it can be used for both legitimate administration and malicious execution.

### Command-Line Analysis

Command-line arguments can reveal what a process actually attempted to execute.

### Encoded PowerShell

Encoded commands can hide the actual PowerShell instructions and therefore require additional investigation.

### Wazuh Investigation

Wazuh provides centralized visibility into endpoint activity, allowing SOC analysts to investigate Windows events from a single dashboard.

---

## Skills Practiced

- Windows Security Event Analysis
- Event ID 4688 Investigation
- Process Creation Monitoring
- Parent-Child Process Analysis
- PowerShell Monitoring
- PowerShell Command-Line Investigation
- Encoded PowerShell Analysis
- Windows Endpoint Monitoring
- Wazuh SIEM Investigation
- SOC Investigation Workflow
- Security Event Analysis

---

## Conclusion

This lab provided practical experience with Windows process creation monitoring and PowerShell investigation using Wazuh.

The investigation demonstrated how a SOC analyst can start with a Windows process creation event and progressively analyze:

Event
↓
Process
↓
Parent Process
↓
Command Line
↓
PowerShell Activity
↓
Encoded Command
↓
Decoded Command
↓
Security Context

Understanding these relationships is important for detecting and investigating suspicious activity on Windows endpoints in a SOC environment.

---

## Screenshots

### 1. Process Creation — 4688
![Process Creation — 4688](./screenshots/01-process-creation-4688.png)

### 2. Process Creation — Command Line Details
![4688 Command Line Process Details](./screenshots/02-4688-command-line-process-details.png)

### 3. Process Creation — 4688 Details
![Process Creation — 4688 Details](./screenshots/02-process-creation-4688-details.png)

### 4. Wazuh — Process Investigation
![Wazuh Process Investigation](./screenshots/03-wazuh-4688-process-investigation.png)

### 5. PowerShell — Command Line
![PowerShell Command Line](./screenshots/04-4688-powershell-command-line.png)

### 6. Wazuh — PowerShell Process Detection
![Wazuh PowerShell Process Detection](./screenshots/04-wazuh-powershell-process-detection.png)

### 7. Wazuh — Encoded PowerShell Detection
![Wazuh Encoded PowerShell Detection](./screenshots/05-wazuh-encoded-powershell.png)

### 8. PowerShell — Command
![PowerShell Command](./screenshots/06-wazuh-powershell-command.png)

### 9. Wazuh — Encoded PowerShell Detection
![Wazuh Encoded PowerShell Detection](./screenshots/07-wazuh-encoded-powershell-detection.png)

### 10. PowerShell — Decoded Command
![Decoded PowerShell Command](./screenshots/08-powershell-command-decoded.png)
### 9. Wazuh — Encoded PowerShell Detection
![Wazuh Encoded PowerShell Detection](./screenshots/07-wazuh-encoded-powershell-detection.png)

### 10. PowerShell — Decoded Command
![Decoded PowerShell Command](./screenshots/08-powershell-command-decoded.png)
