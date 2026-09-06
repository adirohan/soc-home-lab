## 05.1 Failed Windows Logon Detection

I generated controlled failed login attempts on the Windows endpoint (SOC-WIN01) using an incorrect password.

Wazuh successfully detected the authentication failures and generated multiple events with the rule:

- Rule: Logon Failure - Unknown user or bad password
- Rule ID: 60122
- Rule Level: 5
- Agent: SOC-WIN01

### Evidence

![Failed Windows Logon Detection](screenshots/01-failed-logon-detection.png)


## 05.2 Successful Windows Logon Detection

A successful interactive logon was observed on the Windows endpoint.

Wazuh recorded the event as:

- Event: Windows Workstation Logon Success
- Rule ID: 60118
- Rule Level: 3
- Agent: SOC-WIN01

A related special privilege assignment event was also observed.

### Evidence

![Successful Windows Logon Detection](screenshots/02-successful-logon-detection.png)
