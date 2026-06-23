# Day 4 – Ubuntu Server Installation

## Objective

Install Ubuntu Server in a separate UTM virtual machine and verify:

* Successful installation
* User account creation
* Network connectivity
* Basic Linux command functionality

This VM will later be used for Linux administration, networking, security, and server management practice.

---

## Environment

### Host Machine

* MacBook Pro M5 Pro
* 24 GB RAM
* macOS

### Virtualization Platform

* UTM
* QEMU ARM Virtual Machine

### Guest Operating System

* Ubuntu Server 26.04 LTS ARM64

---

## Initial Installation Issue

### Problem

Attempted to install:

```text
ubuntu-26.04-live-server-amd64.iso
```

UTM displayed an architecture compatibility warning:

> The selected boot image contains the word "amd64" but the guest architecture is ARM64 (aarch64).

### Cause

The MacBook uses Apple Silicon (ARM architecture).

The downloaded Ubuntu image was intended for Intel/AMD x86_64 systems.

### Resolution

Downloaded the ARM64 version instead:

```text
ubuntu-26.04-live-server-arm64.iso
```

Installation proceeded successfully.

---

## Ubuntu Server Installation

Created a new Linux VM in UTM with:

* 4 GB RAM
* ARM64 architecture
* Ubuntu Server 26.04 ARM64 ISO

Configured:

* Username: Lucas
* Hostname: ubuntu-server

Completed installation successfully.

---

## Post-Installation Verification

### Hostname Check

**Command:**

```bash
hostname
```

**Output:**

```text
ubuntu-server
```

Verified hostname was configured correctly.

---

### Network Verification

**Command:**

```bash
ip addr
```

**Interface:**

```text
enp0s1
```

**IPv4 Address:**

```text
192.168.64.4
```

Confirmed DHCP network connectivity.

---

### Working Directory Check

**Command:**

```bash
pwd
```

**Output:**

```text
/home/lucas
```

Confirmed user home directory.

---

### Directory Listing

**Command:**

```bash
ls
```

Verified command execution and shell functionality.

---

## Linux Commands Tested

```bash
hostname
ip addr
pwd
ls
```

All commands executed successfully.

---

## Safe Shutdown Procedure

To avoid filesystem corruption or boot issues, shut down Ubuntu from inside the operating system.

**Command:**

```bash
sudo poweroff
```

**Alternative:**

```bash
sudo shutdown now
```

Wait until Ubuntu finishes shutting down before closing the VM.

### Important

Do **NOT**:

* Force quit the VM
* Close the VM window while Ubuntu is running
* Use hard power-offs unnecessarily

Using proper shutdown procedures reduces the risk of boot failures and filesystem corruption.

---

## Lessons Learned

* Apple Silicon Macs require ARM64 operating systems.
* AMD64/x86 ISOs are not compatible with ARM64 virtual machines.
* Ubuntu Server can be administered entirely from the terminal.
* Network configuration can be verified using `ip addr`.
* Proper shutdown procedures are important for VM stability.

---

## Outcome

Successfully installed:

* Ubuntu Server 26.04 ARM64
* User account configured
* Network connectivity verified
* Basic Linux commands tested
* Safe shutdown procedure documented

Ubuntu Server VM is now ready for Linux administration and cybersecurity lab activities.
