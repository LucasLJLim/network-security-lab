# Network Security Home Lab

A self-directed home lab documenting hands-on work in networking, Linux administration, traffic analysis, and Windows Server infrastructure. Each day is a standalone write-up covering the objective, the steps taken, what went wrong, and what I learned.

The lab began on an Apple Silicon Mac running virtual machines in UTM, and later extended into Microsoft Azure to build an Active Directory environment that was impractical to virtualise locally.

---

## Lab Environment

**Host**
- MacBook (Apple Silicon), macOS
- UTM hypervisor

**Virtual machines**
- Windows 11 ARM64 (analysis and client machine)
- Ubuntu Server LTS ARM64 (target and services machine)
- Networked on UTM's shared network, `192.168.64.0/24`

**Cloud**
- Microsoft Azure: Windows Server 2025 domain controller, administered over RDP

**Tools used across the lab**
Wireshark, Nmap, UFW, OpenSSH, Python HTTP server, Server Manager, Active Directory Users and Computers

---

## Lab Index

| Day | Lab | Focus |
|-----|-----|-------|
| [1](docs/day1-setup.md) | UTM Windows 11 ARM Setup | Virtualisation, VM provisioning |
| [2](docs/day2-utm-troubleshooting.md) | VM Troubleshooting and Recovery | Boot failure diagnosis, recovery |
| [3](docs/day3-tools.md) | Cybersecurity Tool Installation | Toolchain setup |
| [4](docs/day4-ubuntu-server.md) | Ubuntu Server Installation | Linux install, CPU architecture |
| [5](docs/day5-linux-administration.md) | Linux Administration Fundamentals | Users, permissions, file management |
| [6](docs/day6-linux-networking.md) | Linux Networking and Remote Access | Interfaces, routing, services |
| [7](docs/day7-nmap-scanning.md) | Nmap Scanning Lab | Network reconnaissance, port scanning |
| [8](docs/day8-wireshark-network-traffic-analysis.md) | Wireshark Network Traffic Analysis | ICMP, DNS, TCP packet analysis |
| [9](docs/day9-http-traffic-analysis.md) | HTTP Traffic Analysis | Cleartext protocol inspection |
| [10](docs/day10-linux-firewall-hardening.md) | Linux Firewall and Service Hardening | UFW rules, access control |
| [11](docs/day11-https-tls-analysis.md) | HTTPS and TLS Analysis | TLS handshake, certificates |
| [12](docs/day12-ssh-remote-administration.md) | Secure Remote Access with SSH | Encrypted remote administration |
| [13](docs/day13-ssh-key-authentication.md) | SSH Key-Based Authentication | Public-key cryptography |
| [14](docs/day14-active-directory-setup.md) | Active Directory on Windows Server (Azure) | Domain controller, DNS, OUs, groups |

---

## Skills Demonstrated

**Networking**
Packet capture and protocol analysis with Wireshark, port scanning and service detection with Nmap, IP addressing and routing, DNS resolution, TCP and TLS session behaviour

**Security**
Firewall configuration and rule design, encrypted versus cleartext protocol comparison, certificate inspection, SSH key-based authentication, network security group rules and least-privilege access

**Linux administration**
User and permission management, service management, network configuration, command-line operations, package management

**Windows and infrastructure**
Windows Server role installation, Active Directory Domain Services, domain controller promotion, DNS integration, organizational unit design, users and security groups

**Cloud**
Azure VM provisioning, virtual networks and subnets, network security groups, remote administration over RDP, cost management with sizing and auto-shutdown

**Practice**
Technical documentation, troubleshooting and root-cause analysis, reproducible step-by-step write-ups

---

## Selected Troubleshooting

Real problems encountered and resolved, documented in the relevant day:

- **EFI boot failure** after an improper VM shutdown, recovered without reinstalling (Day 2)
- **Wrong CPU architecture** — downloaded an amd64 ISO for an ARM64 VM and diagnosed the resulting install failure (Day 4)
- **HTTP 304 versus 200** — traced why a cached response looked like a missing request during traffic capture (Day 9)
- **Azure VM size unavailable** — hit a subscription quota limit and resolved it by changing region and instance size (Day 14)
- **RDP lockout (error 0x204)** — locked myself out by tightening network security group rules, then diagnosed and corrected the rule (Day 14)

---

## Repository Structure

```
.
├── docs/          One markdown write-up per lab day
├── screenshots/   Supporting screenshots, organised by day
└── README.md
```

---