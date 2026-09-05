# 04 - Controlled Activity

In this part of the lab, I started generating some controlled activity on the Windows 11 machine from my Kali machine.

The main purpose was to create normal test activity and then check whether it could be seen from the SOC side.

## Lab Machines

- Kali Linux: 192.168.56.101
- Windows 11 endpoint: 192.168.56.102
- Wazuh server: 192.168.56.20

## Objective

The goal of this section is to generate a few controlled activities on the Windows endpoint and observe the logs and alerts produced by them.

I will document the commands I used, what happened on the Windows machine, and what I could see from the SOC/Wazuh side.

---

## 1. Controlled Network Activity

I started with a simple network activity from the Kali machine against the Windows endpoint.

### Command

```bash
ping -c 4 192.168.56.102
```
## Observation

The ping was successful and all four packets were received with 0% packet loss. This confirmed that the Windows endpoint was reachable from the Kali machine over the lab network.

The average response time was around 1.79 ms.

## Screenshot

![Ping network activity](./screenshots/01-ping-network-activity.png)
