# 08 — Process Creation & PowerShell Detection

## Objective

The objective of this lab was to enable Windows process creation auditing, generate controlled PowerShell activity, collect Windows Security Event ID 4688 telemetry, and verify that Wazuh can capture and expose PowerShell command-line activity for SOC investigation.

The investigation workflow demonstrated in this lab is:

Process Execution → Windows Event 4688 → Wazuh Collection → Detection → Investigation → Command Decoding

---

## Lab Environment

- Windows Endpoint: SOC-WIN01
- Windows IP: 192.168.56.102
- Wazuh Manager: SOC-Wazuh1
- Monitoring Agent: Wazuh Agent
- Event Log: Windows Security
- Primary Event ID: 4688 — A new process has been created

---

## 1. Enable Process Creation Auditing

Windows Process Creation auditing was enabled to generate Security Event ID 4688 whenever a new process is created.

The configuration was verified using:

    auditpol /get /subcategory:"Process Creation"

The result confirmed:

    Process Creation    Success and Failure

This confirmed that Windows was configured to record process creation activity.

---

## 2. Generate a Normal Process Creation Event

A controlled Notepad process was started:

    Start-Process notepad.exe

The resulting Windows Security Event ID 4688 was retrieved and examined.

The event provided process information including:

- New Process Name
- New Process ID
- Creator Process Name
- Process Command Line
- User context

This confirmed that Windows was recording process creation activity.

---

## 3. Verify Process Creation Through Wazuh

The Windows Security telemetry was collected by the Wazuh agent and displayed in Wazuh Threat Hunting.

The Wazuh event showed the Windows endpoint and process information, including:

- Agent name
- New process name
- Parent process
- Process ID
- Command line
- User information

This confirmed successful collection of Windows process creation telemetry by Wazuh.

---

## 4. Generate Controlled PowerShell Activity

A controlled PowerShell command was executed:

    powershell.exe -NoProfile -Command "Write-Output 'SOC-LAB-PS-DETECTION'"

The resulting Event ID 4688 contained PowerShell process and command-line information.

The captured telemetry included:

- PowerShell executable path
- Command line
- Parent process
- Subject/user information
- Process ID

This demonstrated that PowerShell execution could be investigated through Windows process creation telemetry.

---

## 5. Execute an Encoded PowerShell Command

A controlled Base64-encoded PowerShell command was executed:

    powershell.exe -NoProfile -EncodedCommand VwByAGkAdABlAC0ATwB1AHQAcAB1AHQAIAAiAFMATwBDAC0ATABBAEIALQBFAE4AQwBPAEQARQBEACIA

The command produced:

    SOC-LAB-ENCODED

This demonstrated how an encoded PowerShell command appears in process creation telemetry.

---

## 6. Detect Encoded PowerShell in Wazuh

Wazuh Threat Hunting was used to search for PowerShell commands containing the EncodedCommand parameter.

The query used was:

    data.win.eventdata.commandLine:*EncodedCommand*

The resulting Wazuh event showed:

- newProcessName: powershell.exe
- commandLine containing -EncodedCommand
- Parent process information
- Endpoint information
- User context

This demonstrated how a SOC analyst can search process telemetry for encoded PowerShell activity.

---

## 7. Investigate the PowerShell Command Line

The Wazuh event was examined using relevant process creation fields:

- agent.name
- data.win.eventdata.newProcessName
- data.win.eventdata.commandLine
- data.win.eventdata.parentProcessName
- data.win.eventdata.subjectUserName

The command-line field provided the encoded PowerShell payload that was executed on the Windows endpoint.

This information can be used by a SOC analyst to determine what process was executed, by whom, and with what command-line arguments.

---

## 8. Decode the PowerShell Command

The Base64 payload was decoded using PowerShell:

    [System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String("VwByAGkAdABlAC0ATwB1AHQAcAB1AHQAIAAiAFMATwBDAC0ATABBAEIALQBFAE4AQwBPAEQARQBEACIA"))

The decoded command was:

    Write-Output "SOC-LAB-ENCODED"

This demonstrated the investigation process of taking an encoded command observed in telemetry and decoding it to understand the underlying command.

---

## Evidence

The following screenshots document the practical:

- 01-process-creation-audit.png — Process Creation auditing enabled
- 02-normal-process-4688.png — Windows Event ID 4688 for process creation
- 03-wazuh-powershell-process.png — Wazuh PowerShell process telemetry
- 04-powershell-encoded-command.png — Controlled encoded PowerShell execution
- 05-wazuh-encoded-powershell.png — Wazuh captured encoded PowerShell command
- 06-wazuh-powershell-command.png — Wazuh captured normal PowerShell command
- 07-wazuh-encoded-powershell-detection.png — Wazuh filtering for EncodedCommand
- 08-powershell-command-decoded.png — Decoded PowerShell command

---

## SOC Investigation Takeaway

Process creation telemetry is valuable in a SOC because it provides visibility into programs being executed on endpoints.

When investigating suspicious PowerShell activity, an analyst can examine:

1. Which endpoint generated the event
2. Which user executed the process
3. Which process was created
4. Which parent process created it
5. What command line was executed
6. Whether the command was encoded
7. What the encoded command contained

Encoded PowerShell is not automatically malicious. It can have legitimate uses, but encoded commands are an important investigation indicator when combined with suspicious users, parent processes, command-line arguments, or other malicious activity.

---

## Skills Demonstrated

- Windows Security Event Log analysis
- Event ID 4688 investigation
- Process creation auditing
- PowerShell telemetry analysis
- Wazuh Threat Hunting
- Command-line investigation
- Encoded PowerShell identification
- Base64 decoding
- Basic SOC investigation workflow

---

## Conclusion

This practical demonstrated end-to-end process creation monitoring on a Windows endpoint.

Windows generated Event ID 4688 for process creation, Wazuh collected the telemetry, PowerShell command-line activity was identified, encoded PowerShell activity was searched in Wazuh, and the encoded payload was decoded to determine the original command.

This provides a practical foundation for investigating suspicious PowerShell execution in a SOC environment.
