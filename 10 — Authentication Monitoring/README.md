# Practical 2 — Remote Desktop (RDP) Authentication Monitoring

## Objective

The objective of this practical was to generate a legitimate Remote Desktop Protocol (RDP) authentication event and investigate how Windows records the authentication and how Wazuh detects and enriches the event.

The investigation focused on:

- RDP connectivity
- Remote Desktop authentication
- Windows Event ID 4624
- Logon Type 10
- Authenticated user
- Source IP address
- Authentication package
- Logon process
- Wazuh detection
- MITRE ATT&CK context

---

## Lab Environment

| Component | Details |
|---|---|
| SOC Server | Ubuntu + Wazuh |
| Windows Endpoint | SOC-WIN01 |
| Windows IP | 192.168.56.102 |
| Windows User | vboxuser |
| RDP Client | Kali Linux |
| Windows RDP Port | TCP 3389 |
| Windows Event | 4624 |
| Logon Type | 10 — Remote Interactive |
| Wazuh Rule | 92653 |

---

## 1. Verify Remote Desktop Service

Remote Desktop Services was verified on the Windows endpoint using PowerShell.

Command executed:

`Get-Service TermService`

The service was confirmed to be running.

![Remote Desktop Service Verification](./screenshots/authentication-monitoring-practical-2-rdp-service.png)

---

## 2. Verify RDP Firewall Rules

Windows Firewall rules for Remote Desktop were checked to confirm that the RDP service was allowed through the firewall.

The following rules were enabled:

- Remote Desktop - User Mode (TCP-In)
- Remote Desktop - User Mode (UDP-In)
- Remote Desktop - Shadow (TCP-In)

![RDP Firewall Rules](./screenshots/authentication-monitoring-practical-2-rdp-firewall.png)

---

## 3. Verify RDP Port

The Windows endpoint was checked to confirm that TCP port 3389 was listening for Remote Desktop connections.

Command executed:

`Get-NetTCPConnection -LocalPort 3389 -State Listen`

The RDP service was successfully confirmed to be listening on port 3389.

![RDP Port Verification](./screenshots/authentication-monitoring-practical-2-rdp-port.png)

---

## 4. Test RDP Port Connectivity from Kali

Before establishing an RDP session, network-level connectivity to the Windows RDP service was tested from Kali Linux.

Command executed:

`nc -zv 192.168.56.102 3389`

The result showed:

`192.168.56.102 3389 (ms-wbt-server) open`

This confirmed that TCP port 3389 was reachable and that the Windows RDP service was accepting connections.

This command only tested network connectivity to the RDP port. It did not authenticate a user or establish an RDP session.

![RDP Port Connectivity Test](./screenshots/authentication-monitoring-practical-2-rdp-connectivity.png)

---

## 5. Verify the RDP Client

The FreeRDP client installed on Kali Linux was verified before establishing the remote session.

Command executed:

`xfreerdp --version`

The installed FreeRDP version was displayed successfully.

![FreeRDP Version Check](./screenshots/authentication-monitoring-practical-2-rdp-client.png)

---

## 6. Establish the RDP Connection

After confirming network connectivity and verifying the RDP client, an actual RDP connection was initiated from Kali Linux to the Windows endpoint.

Command executed:

`xfreerdp /v:192.168.56.102 /u:vboxuser`

The `vboxuser` account was used for the Windows authentication.

This was the actual remote connection step, unlike the previous `nc` command which only tested port connectivity.

![RDP Connection Command](./screenshots/authentication-monitoring-practical-2-rdp-command.png)

---

## 7. Establish the Remote Desktop Session

The RDP connection successfully opened a Windows desktop session from the Kali Linux environment.

The session was used to generate legitimate Windows authentication telemetry for SOC monitoring.

![RDP Session](./screenshots/authentication-monitoring-practical-2-rdp-session.png)

---

## 8. Hunt for Remote Interactive Authentication in Wazuh

After establishing the RDP session, Wazuh Threat Hunting was used to search for Windows authentication events with Logon Type 10.

The following filter was used:

`data.win.eventdata.logonType:10`

Wazuh returned matching events from the `SOC-WIN01` endpoint.

The relevant detection showed:

`User: WORKGROUP\vboxuser logged using Remote Desktop Connection (RDP)`

![Wazuh RDP Detection](./screenshots/authentication-monitoring-practical-2-wazuh-detection.png)

---

## 9. Investigate the Windows Authentication Event

The detected event was opened in Wazuh Document Details for detailed investigation.

Important fields identified from the event included:

| Field | Value |
|---|---|
| Windows Event ID | 4624 |
| Logon Type | 10 |
| Target Username | vboxuser |
| Source IP | 192.168.56.103 |
| Authentication Package | Negotiate |
| Logon Process | User32 |
| Channel | Security |
| Severity | AUDIT_SUCCESS |
| Workstation | SOC-WINDOWS |
| Agent | SOC-WIN01 |

![Authentication Event Details](./screenshots/authentication-monitoring-practical-2-rule-mitre.png)

---

## 10. Confirm Logon Type 10

The Windows Security event confirmed:

- Event ID: `4624`
- Logon Type: `10`
- Target User: `vboxuser`
- Source IP: `192.168.56.103`

Logon Type 10 identifies a Remote Interactive authentication associated with Remote Desktop Services.

![Logon Type 10 Evidence](./screenshots/authentication-monitoring-practical-2-rule-mitre.png)

---

## 11. Wazuh Detection Rule

Wazuh generated Rule ID `92653`.

Detection description:

`User: WORKGROUP\vboxuser logged using Remote Desktop Connection (RDP) from ip:192.168.56.103.`

The Wazuh event also contained MITRE ATT&CK references:

- `T1021.001` — Remote Desktop Protocol
- `T1078.002` — Valid Accounts: Domain Accounts

![Wazuh Rule and MITRE Mapping](./screenshots/authentication-monitoring-practical-2-rule-mitre.png)

---

## 12. Authentication Investigation Workflow

The complete investigation workflow was:

RDP Port Connectivity Test
→ FreeRDP Client Verification
→ RDP Connection
→ Windows Authentication
→ Windows Event ID 4624
→ Logon Type 10
→ User Identification
→ Source IP Identification
→ Wazuh Detection
→ MITRE ATT&CK Context
→ SOC Investigation

---

## Key Findings

- TCP port 3389 on the Windows endpoint was reachable from Kali.
- FreeRDP was available on the Kali system.
- An actual RDP connection was established using the `vboxuser` account.
- Windows generated a successful authentication event.
- Windows Event ID `4624` confirmed successful authentication.
- Logon Type `10` identified the authentication as Remote Interactive.
- The authenticated account was `vboxuser`.
- The Windows event recorded source IP `192.168.56.103`.
- Wazuh detected the activity using Rule ID `92653`.
- The Wazuh detection identified the activity as an RDP authentication.
- The event contained MITRE ATT&CK references.

---

## SOC Analyst Takeaway

A successful remote authentication should not automatically be considered malicious.

A SOC analyst should investigate the authentication context, including:

- Who authenticated?
- From where?
- When did the authentication occur?
- What logon type was used?
- Was RDP expected in the environment?
- Which authentication mechanism was used?
- What activity occurred after the login?
- Does the activity match expected behavior?

This practical demonstrates how a SOC analyst can move from network-level connectivity verification to actual remote authentication telemetry and finally investigate the resulting Windows event through Wazuh.

---

## Evidence

All practical screenshots are stored in:

`./screenshots/`

Evidence includes:

- `authentication-monitoring-practical-2-rdp-service.png`
- `authentication-monitoring-practical-2-rdp-firewall.png`
- `authentication-monitoring-practical-2-rdp-port.png`
- `authentication-monitoring-practical-2-rdp-connectivity.png`
- `authentication-monitoring-practical-2-rdp-client.png`
- `authentication-monitoring-practical-2-rdp-command.png`
- `authentication-monitoring-practical-2-rdp-session.png`
- `authentication-monitoring-practical-2-wazuh-detection.png`
- `authentication-monitoring-practical-2-event-details.png`
- `authentication-monitoring-practical-2-logon-type-10.png`
- `authentication-monitoring-practical-2-rule-mitre.png`

---

## Practical Status

**COMPLETED**

The practical successfully demonstrated the complete remote authentication monitoring workflow:

RDP Connectivity
→ RDP Client
→ Remote Desktop Session
→ Windows Authentication
→ Event ID 4624
→ Logon Type 10
→ Wazuh Detection
→ Event Investigation
→ MITRE ATT&CK Context
