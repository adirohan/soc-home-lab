## 05.1 Failed Windows Logon Detection

I generated controlled failed login attempts on the Windows endpoint (SOC-WIN01) using an incorrect password.

Wazuh successfully detected the authentication failures and generated multiple events with the rule:

- Rule: Logon Failure - Unknown user or bad password
- Rule ID: 60122
- Rule Level: 5
- Agent: SOC-WIN01

### Evidence

![Failed Windows Logon Detection](screenshots/01-failed-logon-detection.png)
