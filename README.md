# Open Source Firewall Analysis

**Module:** Network Security — BSc. Computing in Digital Forensics and Cyber Security (TU863)

---

## What this is

A practical security report comparing two open source firewalls — **nftables** and **OPNsense** — configured and tested inside an isolated virtual lab environment. The goal was to see how well each firewall holds up against common network attacks when properly configured.

---

## Tools Used

- **Oracle VirtualBox** — for building the isolated lab
- **Kali Linux** — used as both the target/firewall machine and the attacker machine
- **nftables** — Linux kernel-level packet filtering firewall
- **OPNsense** — FreeBSD-based perimeter firewall with a web GUI
- **Nmap** — for port scanning and service detection
- **hping3** — for ICMP flood attacks
- **Apache / SSH / FTP** — services deliberately exposed for testing

---

## Lab Setup

### nftables
Two Kali VMs on the same internal network with no internet access. One VM acted as the firewall/target (192.168.1.1), the other as the attacker (192.168.1.2). Three services were deliberately started before testing — Apache on port 80, SSH on port 22, and FTP on port 21.

### OPNsense
Three VMs — OPNsense sat in the middle as a gateway between the attacker (WAN side) and the target (LAN side), which is how a real perimeter firewall works in practice.

---

## Tests Carried Out

### Test 1 — Port Scanning and Service Detection
Used Nmap to scan for open ports and software versions before and after applying firewall rules. Without rules everything was visible. After rules were applied, unwanted ports were filtered and access was restricted by IP where needed.

### Test 2 — ICMP Flood Attack (nftables)
Used hping3 to flood the target with ~668,000 ICMP packets over 36 seconds while running a normal ping in parallel. Before any rules the system couldn't tell the difference between flood and legitimate traffic — CPU idle dropped from 98.6% to 83.1% during the attack. After applying rate limiting (3 ICMP requests per minute), the flood achieved 100% packet loss while normal pings still got through fine.

### Test 3 — IP-Based Access Control (OPNsense)
Configured OPNsense to allow FTP (port 21) only from a specific trusted IP. Verified access was granted from the trusted IP and blocked from an untrusted one.

---

## Key Findings

| | nftables | OPNsense |
|---|---|---|
| Interface | Command line only | Web-based GUI |
| Deployment | Host-based (on the machine itself) | Perimeter (between networks) |
| Control | Fine-grained, kernel level | Easier to manage visually |
| Port scanning mitigation | Effective | Effective |
| ICMP flood mitigation | Yes (rate limiting) | Not tested |
| Best suited for | Servers needing custom rule logic | Small/medium network perimeter security |

Both firewalls successfully blocked unauthorised access and reduced the attack surface when properly configured. nftables gives more low-level control, OPNsense is more realistic for actual network deployments.

---

## Files

- `open-source-firewall-analysis.pdf` — full report with screenshots and results

---

## Note

This was done in a fully isolated virtual lab. All attacks were run against machines I owned and set up myself.
