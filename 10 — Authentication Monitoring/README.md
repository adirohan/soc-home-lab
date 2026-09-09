# 10 — Authentication Monitoring

## Objective

The objective of this lab was to monitor Windows authentication activity using Wazuh and investigate authentication context beyond simply identifying a successful logon.

The investigation focused on identifying:

- The authenticated user
- Authentication type
- Logon type
- Source address
- Logon session ID
- Authentication package
- Logon process
- Endpoint involved

The goal was to understand how a SOC analyst can use authentication telemetry to establish context around a Windows user session.

---

## Lab Environment

| Component | Details |
|---|---|
| SOC Server | Ubuntu + Wazuh |
| Windows Endpoint | SOC-WIN01 |
| Windows IP | 192.168.56.102 |
| User | vboxuser |
| Log Source | Windows Security Event Log |
| Wazuh Detection | Windows Workstation Logon Success |
| Wazuh Rule | 60118 |
| Windows Event ID | 4624 |

---

# 1. Authentication Monitoring

Authentication monitoring is important in a SOC because user authentication creates evidence about who accessed an endpoint and how the session was established.

Instead of investigating authentication events only as "successful" or "failed", the analyst can examine the surrounding authentication context.

The investigation workflow was:

    Authentication Event
            ↓
    Identify User
            ↓
    Identify Logon Type
            ↓
    Identify Source
            ↓
    Identify Logon Session
            ↓
    Identify Authentication Package
            ↓
    Investigate the Authentication Context

---

# 2. Wazuh Authentication Detection

Wazuh was used to collect Windows Security authentication telemetry from the Windows endpoint.

The relevant Wazuh detection was:

| Field | Value |
|---|---|
| Rule ID | 60118 |
| Rule Description | Windows Workstation Logon Success |
| Rule Level | 3 |
| Windows Event ID | 4624 |
| Agent | SOC-WIN01 |

The event was investigated through:

    Wazuh
    → Threat Hunting
    → Events
    → Windows Workstation Logon Success
    → Document Details

### Evidence

![Wazuh Successful Authentication](10-01-wazuh-successful-authentication.png)

This evidence shows the Windows authentication event collected by Wazuh.

---

# 3. Authentication Context Investigation

The Wazuh event was examined to identify the authentication context.

Important fields included:

| Field | Observed Value |
|---|---|
| Agent Name | SOC-WIN01 |
| Agent IP | 192.168.56.102 |
| Authentication Package | Negotiate |
| Source IP | 127.0.0.1 |
| Logon Process | User32 |
| Logon Type | 2 |
| Target Domain | SOC-WINDOWS |
| Target User | vboxuser |
| Target User SID | S-1-5-21-35272764-2421096956-2582236340-1000 |
| Target Logon ID | 0x44da78 |
| Workstation | SOC-WINDOWS |
| Windows Event ID | 4624 |

The event therefore provided significantly more information than simply showing that authentication succeeded.

### Evidence

![Authentication User Context](10-01-authentication-user-context.png)

This evidence shows the user, session, logon type, workstation and related authentication context.

---

# 4. Logon Type Analysis

The event contained:

    Logon Type: 2

Logon Type 2 represents an **Interactive Logon**.

This is consistent with a local user authentication session rather than a remote RDP session.

From a SOC perspective, the logon type is important because different authentication mechanisms can have different security implications.

For example:

    Logon Type 2
    → Interactive / local user session

    Logon Type 10
    → Remote Interactive / RDP session

The analyst should therefore examine the logon type together with the user, source address and surrounding activity.

---

# 5. User and Session Identification

The authentication event identified:

    Target User:
    vboxuser

The corresponding user SID was:

    S-1-5-21-35272764-2421096956-2582236340-1000

The authentication session was identified using:

    Target Logon ID:
    0x44da78

The Logon ID can be useful during investigation because it provides session context that can be compared with other Windows security telemetry where the same identifier is available.

---

# 6. Authentication Source Analysis

The event reported:

    Source IP:
    127.0.0.1

    Source Port:
    0

The source address is the local loopback address.

This indicates that this particular authentication event was associated with local authentication activity on the Windows endpoint rather than showing a remote network source.

The analyst should therefore avoid interpreting this event as a remote authentication attempt without additional evidence.

---

# 7. Authentication Package and Logon Process

The event reported:

    Authentication Package:
    Negotiate

    Logon Process:
    User32

These fields provide additional context about how Windows processed the authentication request.

Combining these values with the logon type and target account gives the analyst a more complete picture of the authentication event.

---

# 8. SOC Investigation

A SOC analyst should not stop at:

    "4624 = successful login"

Instead, the investigation should answer:

    Who authenticated?
        ↓
    What type of logon occurred?
        ↓
    Was the source local or remote?
        ↓
    What session was created?
        ↓
    Which authentication package was used?
        ↓
    What other activity occurred around the session?

For this lab, the evidence showed:

    User:
    vboxuser

    Logon Type:
    2 — Interactive

    Source:
    127.0.0.1

    Logon ID:
    0x44da78

    Authentication Package:
    Negotiate

    Logon Process:
    User32

This represents a locally observed interactive authentication session on the Windows endpoint.

---

# 9. Investigation Findings

The authentication event was determined to be:

| Investigation Item | Finding |
|---|---|
| Endpoint | SOC-WIN01 |
| User | vboxuser |
| Authentication Result | Successful |
| Windows Event | 4624 |
| Wazuh Rule | 60118 |
| Logon Type | 2 — Interactive |
| Source | 127.0.0.1 |
| Authentication Package | Negotiate |
| Logon Process | User32 |
| Session ID | 0x44da78 |

No suspicious remote authentication source was established from this particular event.

The activity was generated within the controlled home lab environment.

---

# 10. Detection and Investigation Flow

    Windows Authentication
            ↓
    Security Event 4624
            ↓
    Wazuh Agent
            ↓
    Wazuh Manager
            ↓
    Rule 60118
            ↓
    Threat Hunting
            ↓
    Document Details
            ↓
    Identify User
            ↓
    Analyze Logon Type
            ↓
    Analyze Source
            ↓
    Analyze Session
            ↓
    SOC Assessment

---

# 11. Important SOC Concepts Learned

### Authentication Context

Authentication monitoring provides information about the account and session involved in an authentication event.

### Logon Type

The logon type helps distinguish different forms of authentication, such as interactive and remote interactive sessions.

### Logon ID

The Logon ID provides session-specific context that can help correlate related Windows security events.

### Source Address

The source address helps determine whether authentication originated locally or from another host.

### Authentication Package

The authentication package provides additional context about the authentication mechanism used by Windows.

---

# 12. MITRE ATT&CK Context

The Wazuh rule associated with the investigated authentication event included:

    T1078 — Valid Accounts

Authentication telemetry can be useful when investigating the potential use of legitimate accounts for unauthorized access.

In this controlled laboratory exercise, the authentication was authorized and performed by the lab user.

---

# 13. Evidence Screenshots

All evidence screenshots for this section are stored in:

    10 — Authentication Monitoring/screenshots/

### Screenshot 01 — Wazuh Successful Authentication

    10-01-wazuh-successful-authentication.png

Shows the Wazuh Document Details for the Windows authentication event.

### Screenshot 02 — Authentication User Context

    10-01-authentication-user-context.png

Shows the target user, user SID, Logon ID, workstation and related authentication fields.

---

# 14. Detection Summary

| Item | Result |
|---|---|
| Windows authentication telemetry collected | ✅ |
| Event ID 4624 identified | ✅ |
| Wazuh Rule 60118 identified | ✅ |
| Target user identified | ✅ |
| Logon Type identified | ✅ |
| Source address analyzed | ✅ |
| Logon session identified | ✅ |
| Authentication package identified | ✅ |
| Authentication context investigated | ✅ |

---

# Conclusion

This exercise demonstrated authentication monitoring from a SOC investigation perspective.

Rather than treating a successful authentication as a standalone event, the investigation examined the account, logon type, source address, authentication package, logon process, workstation and session identifier.

The investigation established the following authentication context:

    SOC-WIN01
        ↓
    vboxuser
        ↓
    Event ID 4624
        ↓
    Logon Type 2 — Interactive
        ↓
    Source 127.0.0.1
        ↓
    Logon ID 0x44da78
        ↓
    Wazuh Rule 60118
        ↓
    Authentication Context Investigated

This practical demonstrates how a SOC analyst can use Windows authentication telemetry collected by Wazuh to understand user access and establish context for further investigation.
