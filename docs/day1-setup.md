# Day 1 - UTM Setup

## Goal
Set up Windows 11 ARM VM using UTM.

## What I did
- Downloaded Windows 11 ARM64 ISO from Microsoft
- Created UTM VM
- Allocated 6GB RAM, 4 CPU cores, 80GB storage
- Installed Windows 11 Pro

## Problems
Booted into EFI shell because wrong x64 ISO was used.

## Fixes
Downloaded ARM64 ISO instead.

## Result
Windows 11 installed successfully.

## Next Step
Install Python, Wireshark, and project dependencies.