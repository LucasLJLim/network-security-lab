# Day 6 – Linux Networking and Remote Access

## Objective

The objective of this lab was to develop a foundational understanding of Linux networking and remote administration. The tasks focused on examining network configuration, verifying connectivity, inspecting routing information, identifying running services, and installing common networking utilities used in Linux system administration and cybersecurity.

---

## Network Configuration

The network configuration of the Ubuntu Server virtual machine was examined using the `ip addr` command.

Command used:

```bash
ip addr
```

The primary network interface was identified as:

```text
enp0s1
```

The Ubuntu Server VM was assigned the following IPv4 address through DHCP:

```text
192.168.64.4
```

The interface status was confirmed to be **UP**, indicating that the virtual machine was successfully connected to the network.

---

## Routing

The routing table was examined to determine how network traffic leaves the virtual machine.

Command used:

```bash
ip route
```

The routing table showed the default gateway:

```text
default via 192.168.64.1 dev enp0s1
```

This confirmed that outbound traffic is routed through the gateway at **192.168.64.1** using the **enp0s1** network interface.

---

## Connectivity Testing

Internet connectivity was verified using the `ping` command.

Command used:

```bash
ping google.com
```

The Ubuntu Server VM successfully received replies from Google's servers, confirming:

* Internet connectivity
* Proper network configuration
* Successful DNS name resolution

The ping test was stopped using:

```text
Ctrl + C
```

---

## DNS Resolution

Domain Name System (DNS) functionality was tested using the `nslookup` utility.

The required package was installed:

```bash
sudo apt install dnsutils -y
```

Command used:

```bash
nslookup google.com
```

The command successfully resolved **google.com** into its corresponding IP addresses, confirming that DNS services were functioning correctly.

---

## Open Ports

Open network ports and listening services were inspected using:

```bash
ss -tuln
```

The output displayed active TCP and UDP sockets along with the services currently listening for incoming connections.

This command is commonly used by system administrators and cybersecurity professionals to identify exposed services and verify server configurations.

---

## SSH Verification

The status of the Secure Shell (SSH) service was checked to confirm that remote administration was available.

Command used:

```bash
sudo systemctl status ssh
```

The SSH service was confirmed to be:

```text
active (running)
```

This allows secure remote management of the Ubuntu Server.

---

## User Information

Information about the currently logged-in user was verified.

Commands used:

```bash
whoami
```

```bash
id
```

These commands displayed:

* Current username
* User ID (UID)
* Group ID (GID)
* Group memberships

This information is useful for understanding user permissions and privilege management within Linux.

---

## Running Processes

Running processes were examined using:

```bash
ps aux
```

This command displayed currently executing processes, including:

* Running applications
* Background services
* System daemons
* Resource usage

Process monitoring is an important skill for Linux administration and cybersecurity investigations.

---

## Tools Installed

Additional networking utilities were installed.

Command used:

```bash
sudo apt install net-tools traceroute -y
```

The following commands were tested:

```bash
ifconfig
```

```bash
traceroute google.com
```

These tools provide alternative methods for examining network interfaces and tracing network paths between systems.

---

## Lessons Learned

* Linux networking information can be viewed using the `ip` command suite.
* Every Linux system maintains a routing table that determines where network traffic is sent.
* DNS translates domain names into IP addresses.
* Network connectivity can be verified using `ping`.
* Open ports and listening services can be inspected using `ss -tuln`.
* SSH enables secure remote administration of Linux servers.
* User identity and permissions can be viewed using `whoami` and `id`.
* Process monitoring is an essential Linux administration skill.
* Additional networking tools such as `ifconfig` and `traceroute` remain useful for troubleshooting despite newer alternatives.

---

## Outcome

Successfully completed Linux networking fundamentals within the Ubuntu Server virtual machine.

Achievements included:

* Examined network interface configuration
* Identified the system's default gateway
* Verified internet connectivity
* Tested DNS name resolution
* Inspected listening network ports
* Verified SSH service operation
* Examined user information
* Monitored running processes
* Installed additional networking tools

The Ubuntu Server VM is now prepared for more advanced networking, vulnerability assessment, and cybersecurity lab activities, including network scanning with Nmap and packet analysis using Wireshark.
