# Reconnaissance

This was the first step of my lab testing. I used my Kali machine to check what was active on the VirtualBox network and then looked more closely at the Windows 11 endpoint.

## Target

Windows 11 SOC endpoint  
IP: `192.168.56.102`

## 1. Host Discovery

I started with a simple host discovery scan to see which machines were currently active on the lab network.

### Command

```bash
nmap -sn 192.168.56.0/24
Result

The scan found 5 active hosts:

192.168.56.1
192.168.56.20
192.168.56.100
192.168.56.101
192.168.56.102

The Windows 11 machine (192.168.56.102) was detected as up.

This confirmed that my Kali machine could reach the Windows endpoint on the lab network.

2. NULL Scan

I then tried a NULL scan against the lab network to see how the Windows endpoint responded to TCP probes without any TCP flags set.

Command
nmap -sN 192.168.56.0/24

For the Windows endpoint 192.168.56.102, Nmap showed:

All 1000 scanned ports on 192.168.56.102 are in ignored states.
Not shown: 1000 open|filtered tcp ports (no-response)

The important part here was the lack of response from the Windows machine. I did not treat this as proof that all ports were open. It mainly showed that the endpoint was not giving a normal response to these probes.

3. SYN Scan

After that, I checked some common Windows-related ports on the endpoint using a SYN scan.

Command
nmap -sS -Pn --reason -p 135,139,445,3389,5985,5986,80,443 192.168.56.102
Result
80/tcp    filtered  http
135/tcp   filtered  msrpc
139/tcp   filtered  netbios-ssn
443/tcp   filtered  https
445/tcp   filtered  microsoft-ds
3389/tcp  filtered  ms-wbt-server
5985/tcp  filtered  wsman
5986/tcp  filtered  wsmans

All of the tested ports were reported as filtered.

This was useful because it showed that the Windows endpoint was reachable, but these services were not directly responding to my scan. The no-response reason also suggested that something was filtering or dropping the probes.

4. Service Detection

I also ran service detection against the same ports to see if Nmap could identify any services.

Command
nmap -sV -Pn --reason -p 135,139,445,3389,5985,5986,80,443 192.168.56.102

The result was still:

80/tcp    filtered  http
135/tcp   filtered  msrpc
139/tcp   filtered  netbios-ssn
443/tcp   filtered  https
445/tcp   filtered  microsoft-ds
3389/tcp  filtered  ms-wbt-server
5985/tcp  filtered  wsman
5986/tcp  filtered  wsmans

Nmap could not identify a specific service version because the ports were filtered.

Takeaway

The main thing I got from this reconnaissance was that the Windows endpoint was reachable on the lab network, but the common ports I checked were being filtered.

This gives me a baseline before moving on to the next part of the lab, where I can generate controlled activity and see how the SOC monitoring setup detects it.
