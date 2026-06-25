# Day 7 – Nmap Scanning Lab

## Objective

Learn the fundamentals of network reconnaissance by using Nmap from the Windows 11 virtual machine to scan the Ubuntu Server virtual machine.

The goals of this lab were to:

- Verify connectivity between virtual machines
- Discover open network ports
- Identify running network services
- Perform operating system detection
- Understand how Nmap gathers network information

---

## Lab Setup

### Scanner

- Windows 11 ARM Virtual Machine
- Nmap installed

### Target

- Ubuntu Server 26.04 LTS ARM64
- Hostname: `ubuntu-server`
- IP Address: `192.168.64.4`

Both virtual machines were running within UTM using the shared virtual network.

---

## Target IP Address

On the Ubuntu Server VM, the following command was executed:

```bash
ip addr
```

The active network interface (`enp0s1`) was assigned:

```text
192.168.64.4
```

This IP address was used as the target for all Nmap scans.

---

## Connectivity Test

Before performing any scans, connectivity between the Windows VM and Ubuntu Server VM was verified.

Command:

```cmd
ping 192.168.64.4
```

Successful replies confirmed that the Windows VM could communicate with the Ubuntu Server over the virtual network.

---

## Basic Nmap Scan

Command:

```cmd
nmap 192.168.64.4
```

This performed a scan of the most common TCP ports.

The scan identified which ports were open and available on the Ubuntu Server.

Open ports indicate services that are accepting network connections.

---

## Service Version Detection

Command:

```cmd
nmap -sV 192.168.64.4
```

The `-sV` option probes open ports to identify the application and service versions running on the target.

This provides more detailed information than a basic port scan and is commonly used during reconnaissance.

---

## Operating System Detection

Command:

```cmd
nmap -O 192.168.64.4
```

The `-O` option attempts to identify the operating system by analysing responses to specially crafted network packets.

This scan required Command Prompt to be run with Administrator privileges.

Nmap successfully attempted to fingerprint the target operating system.

---

## SSH Port Scan

Command:

```cmd
nmap -p 22 192.168.64.4
```

This scan targeted only TCP port 22.

Port 22 is the default port used by Secure Shell (SSH).

The scan determined whether the SSH service was available on the Ubuntu Server.

---

## Nmap Commands Practised

```cmd
nmap 192.168.64.4
nmap -sV 192.168.64.4
nmap -O 192.168.64.4
nmap -p 22 192.168.64.4
```

---

## Key Concepts Learned

### Nmap

Nmap (Network Mapper) is an open-source network scanning tool used to discover hosts, identify open ports, detect services, and perform operating system fingerprinting.

It is one of the most widely used tools in cybersecurity, penetration testing, and network administration.

### Port Scanning

Port scanning identifies which network ports are open, closed, or filtered on a target system.

Open ports indicate services that are available to receive network connections.

### Service Detection

Service detection identifies the applications running behind open ports, such as SSH, HTTP, FTP, or DNS.

### Operating System Detection

Nmap analyses packet responses to estimate the operating system running on the target device.

### Network Reconnaissance

Reconnaissance is the first phase of many cybersecurity assessments and penetration tests.

The goal is to collect as much information as possible about the target before further testing.

---

## Lessons Learned

- Virtual machines can communicate over a shared virtual network.
- Nmap can quickly identify open ports and running services.
- Different Nmap options provide increasing levels of detail.
- Operating system detection requires Administrator privileges on Windows.
- Reconnaissance is an essential first step in cybersecurity assessments.
- Understanding open ports helps identify potential attack surfaces.

---

## Outcome

Successfully completed the first practical network scanning lab.

Achievements:

- Verified communication between Windows and Ubuntu virtual machines.
- Performed basic TCP port scanning.
- Identified running services using version detection.
- Attempted operating system fingerprinting.
- Scanned specific ports using Nmap.
- Gained practical experience with one of the most widely used cybersecurity tools.

The home lab now supports practical network reconnaissance exercises and provides a strong foundation for future penetration testing and defensive security activities.