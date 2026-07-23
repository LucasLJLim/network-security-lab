# Day 1 - UTM Windows 11 ARM Setup

## Goal

Set up a Windows 11 ARM64 virtual machine using UTM on macOS.

## Configuration

* OS: Windows 11 ARM64
* Hypervisor: UTM
* RAM: 6144 MiB (6 GB)
* CPU: 4 Cores
* Storage: 80 GB

## What I Did

1. Downloaded the Windows 11 ARM64 ISO from Microsoft.
2. Created a new Windows virtual machine in UTM.

![New Windows virtual machine created in UTM](../screenshots/day1/01-utm-created-vm.png)

3. Configured memory, CPU, and storage allocations.

![Memory and CPU allocation configured](../screenshots/day1/02-configured-memory-and-cpu.png)

4. Configured a shared directory between host and guest.

![Shared directory configured](../screenshots/day1/03-configured-shared-directory.png)

5. Started the virtual machine and attempted installation.
6. Troubleshot boot issues.
7. Installed Windows 11 Pro successfully.

## Problems Encountered

### EFI Shell Boot Error

The virtual machine booted into the EFI shell instead of the Windows installer.

![Boot failure dropping into the EFI shell](../screenshots/day1/04-boot-failure-efi-shell.png)

## Resolution

The issue occurred because an x64 Windows ISO was initially used. The ISO was replaced with the correct Windows 11 ARM64 version and the VM was recreated.

Working through the UEFI boot manager to identify the problem:

![Boot manager troubleshooting](../screenshots/day1/05-boot-manager-troubleshooting.png)

![Boot manager troubleshooting, continued](../screenshots/day1/06-boot-manager-troubleshooting-2.png)

With the correct ARM64 ISO attached, the Windows installer loaded:

![Windows 11 installation screen](../screenshots/day1/07-windows-11-installation-screen.png)

## Result

Windows 11 ARM64 installed successfully and booted to the desktop environment.

![Windows 11 installed successfully and booted to desktop](../screenshots/day1/08-successful-windows-11-installation.png)

## Next Steps

* Install Python
* Install Wireshark
* Install project dependencies
* Configure development environment
* Begin project-specific setup