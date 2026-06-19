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
3. Configured memory, CPU, and storage allocations.
4. Started the virtual machine and attempted installation.
5. Troubleshot boot issues.
6. Installed Windows 11 Pro successfully.

## Problems Encountered

### EFI Shell Boot Error

The virtual machine booted into the EFI shell instead of the Windows installer.

## Resolution

The issue occurred because an x64 Windows ISO was initially used. The ISO was replaced with the correct Windows 11 ARM64 version and the VM was recreated.

## Result

Windows 11 ARM64 installed successfully and booted to the desktop environment.

## Evidence

Screenshots stored in the `/screenshots` directory:

* 01-utm-created-vm.png
* 02-configured-memory-and-cpu.png
* 03-configured-shared-directory.png
* 04-boot-failure-efi-shell.png
* 05-boot-manager-troubleshooting.png
* 06-boot-manager-troubleshooting-2.png
* 07-windows-11-installation-screen.png
* 08-successful-windows-11-installation.png

## Next Steps

* Install Python
* Install Wireshark
* Install project dependencies
* Configure development environment
* Begin project-specific setup