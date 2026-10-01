# Home-Lab

# macOS Virtualization Lab

## Installing UTM, Kali Linux, and Windows 11 Virtual Machines on macOS

This project documents how to build a local virtualization lab on macOS using **UTM**, then create and configure:

- A Kali Linux virtual machine
- A Windows 11 virtual machine

The lab can be used for:

- Cybersecurity practice
- Penetration-testing labs
- Windows administration
- Linux administration
- Networking experiments
- Malware-analysis labs with proper isolation
- Software testing
- Learning virtualization

---

# Table of Contents

1. [Architecture Overview](#1-architecture-overview)
2. [Requirements](#2-requirements)
3. [Check Your Mac Architecture](#3-check-your-mac-architecture)
4. [Install UTM](#4-install-utm)
5. [Download Operating System Images](#5-download-operating-system-images)
6. [Create the Kali Linux VM](#6-create-the-kali-linux-vm)
7. [Install Kali Linux](#7-install-kali-linux)
8. [Configure Kali Linux](#8-configure-kali-linux)
9. [Create the Windows 11 VM](#9-create-the-windows-11-vm)
10. [Install Windows 11](#10-install-windows-11)
11. [Install Windows Guest Tools](#11-install-windows-guest-tools)
12. [Configure VM Networking](#12-configure-vm-networking)
13. [Test Communication Between the VMs](#13-test-communication-between-the-vms)
14. [Create Snapshots or Backups](#14-create-snapshots-or-backups)
15. [Recommended Lab Configuration](#15-recommended-lab-configuration)
16. [Troubleshooting](#16-troubleshooting)
17. [Security Considerations](#17-security-considerations)
18. [Validation Checklist](#18-validation-checklist)
19. [Project Result](#19-project-result)

---

# 1. Architecture Overview

The environment consists of a macOS host running two guest operating systems.

```text
                    macOS Host
                        |
                        |
                       UTM
                  /           \
                 /             \
                /               \
        Kali Linux VM       Windows 11 VM
        ARM64 / AMD64       ARM64 / x64
```

UTM provides the interface used to create and manage the virtual machines.

The correct guest architecture depends on your Mac.

| Mac | Kali Linux | Windows |
|---|---|---|
| Apple Silicon M1/M2/M3/M4/M5 | ARM64 | Windows 11 ARM64 |
| Intel Mac | AMD64 / x86_64 | Windows 11 x64 |

Using a guest architecture that matches the host CPU provides substantially better performance than CPU emulation.

---

# 2. Requirements

Before starting, make sure the Mac has enough resources.

## Minimum recommended hardware

```text
RAM:        16 GB recommended
Storage:    100+ GB free space recommended
CPU:        Apple Silicon or Intel 64-bit processor
macOS:      Modern supported macOS release
Internet:   Required for downloads and updates
```

8 GB of RAM can work, but running macOS, Kali, and Windows simultaneously will be constrained.

## Suggested resource allocation

### Kali Linux

```text
CPU:       2-4 cores
RAM:       4 GB
Disk:      40-60 GB
Network:   Shared/NAT
```

### Windows 11

```text
CPU:       4 cores
RAM:       6-8 GB
Disk:      64-100 GB
Network:   Shared/NAT
```

Do not allocate all available CPU cores or RAM to virtual machines. macOS also needs resources to operate normally.

---

# 3. Check Your Mac Architecture

Before downloading Kali or Windows, determine whether the Mac uses Apple Silicon or Intel.

Open Terminal:

```bash
uname -m
```

Possible outputs:

### Apple Silicon

```text
arm64
```

Use:

```text
Kali Linux ARM64
Windows 11 ARM64
```

### Intel

```text
x86_64
```

Use:

```text
Kali Linux AMD64
Windows 11 x64
```

You can also check from:

```text
Apple Menu
    ↓
About This Mac
```

Apple Silicon Macs show a chip such as:

```text
Apple M1
Apple M2
Apple M3
Apple M4
Apple M5
```

Intel Macs display an Intel processor.

---

# 4. Install UTM

UTM is a virtualization and emulation application for macOS built around technologies including QEMU and Apple's virtualization capabilities.

## Step 1 — Download UTM

Download the current stable version from the official UTM website or GitHub release.

Two versions are generally available:

```text
GitHub version
Mac App Store version
```

The GitHub release is free to download.

The App Store version helps financially support the project and provides convenient automatic updates.

---

## Step 2 — Install UTM

If using the DMG version:

1. Open the downloaded `.dmg`.
2. Drag **UTM** into the **Applications** folder.
3. Open:

```text
Applications → UTM
```

macOS may ask for permission to open an application downloaded from the Internet.

Select:

```text
Open
```

UTM should now display its main virtual-machine window.

---

# 5. Download Operating System Images

Before creating the VMs, download the installation media.

---

## Kali Linux

Download the current **Installer** image from the official Kali Linux website.

### Apple Silicon

Choose:

```text
Installer
ARM64
```

The filename will look similar to:

```text
kali-linux-<version>-installer-arm64.iso
```

### Intel

Choose:

```text
Installer
AMD64
```

The filename will look similar to:

```text
kali-linux-<version>-installer-amd64.iso
```

Do not download the AMD64 installer for an Apple Silicon VM unless you specifically intend to emulate an x86 system.

---

## Windows 11

Download Windows directly from Microsoft.

### Apple Silicon

Download:

```text
Windows 11 ARM64 ISO
```

### Intel

Download:

```text
Windows 11 x64 ISO
```

A valid Windows license may be required for activation.

---

# 6. Create the Kali Linux VM

Open UTM.

Click:

```text
+
```

Select:

```text
Create a New Virtual Machine
```

---

## Step 1 — Select virtualization

Choose:

```text
Virtualize
```

rather than:

```text
Emulate
```

when the Kali architecture matches the Mac architecture.

For example:

```text
Apple Silicon Mac + Kali ARM64 = Virtualize
Intel Mac + Kali AMD64         = Virtualize
```

---

## Step 2 — Select operating system

Choose:

```text
Linux
```

---

## Step 3 — Select the Kali ISO

Click:

```text
Browse
```

Select the downloaded Kali installer.

Example:

```text
kali-linux-<version>-installer-arm64.iso
```

or:

```text
kali-linux-<version>-installer-amd64.iso
```

---

## Step 4 — Configure hardware

A good starting configuration is:

```text
RAM:       4096 MB
CPU cores: 4
```

For an 8 GB Mac, consider:

```text
RAM:       2048-3072 MB
CPU cores: 2
```

For a 16+ GB Mac:

```text
RAM:       4096-8192 MB
CPU cores: 4
```

---

## Step 5 — Configure storage

Create a virtual disk.

Recommended:

```text
40 GB minimum
```

A better size for a cybersecurity lab is:

```text
60 GB
```

The virtual disk is normally dynamically allocated, so the entire configured capacity is not necessarily consumed immediately.

---

## Step 6 — Shared directory

UTM may offer to configure a shared directory.

You can skip this for now.

For security labs, keeping host and guest files separated initially is often preferable.

---

## Step 7 — Name the VM

Use something descriptive:

```text
Kali-Linux-Lab
```

Click:

```text
Save
```

---

# 7. Install Kali Linux

Start:

```text
Kali-Linux-Lab
```

The Kali installer should boot.

Select:

```text
Graphical install
```

---

## Step 1 — Language

Choose your preferred language.

Example:

```text
English
```

---

## Step 2 — Location

Select your country or region.

---

## Step 3 — Keyboard

Choose your keyboard layout.

Example:

```text
American English
```

---

## Step 4 — Hostname

Example:

```text
kali
```

---

## Step 5 — Domain

For a local laboratory, this can normally remain empty.

---

## Step 6 — Create user

Example:

```text
Full name: Kali User
Username:  kali
```

Create a strong password.

Do not use passwords such as:

```text
password
123456
kali
admin
```

---

## Step 7 — Disk partitioning

Select:

```text
Guided - use entire disk
```

This refers to the **virtual disk**, not the physical Mac disk.

Then select the virtual disk.

For a simple lab environment choose:

```text
All files in one partition
```

Select:

```text
Finish partitioning and write changes to disk
```

Confirm:

```text
Yes
```

---

## Step 8 — Software selection

The default Kali configuration is appropriate for most users.

Keep the graphical desktop selected.

Kali normally uses Xfce as its default desktop environment.

Continue the installation.

---

## Step 9 — Finish installation

When installation completes:

```text
Continue
```

The VM should reboot.

If the installer starts again, shut down the VM and remove/eject the Kali ISO from the virtual CD/DVD drive.

Then boot the VM again.

You should now reach the installed Kali system.

---

# 8. Configure Kali Linux

Log into Kali.

First update the operating system.

Open Terminal:

```bash
sudo apt update
```

Then:

```bash
sudo apt full-upgrade -y
```

Reboot:

```bash
sudo reboot
```

---

## Install SPICE integration

For improved clipboard and display integration:

```bash
sudo apt install spice-vdagent -y
```

Optionally install the QEMU guest agent:

```bash
sudo apt install qemu-guest-agent -y
```

Reboot:

```bash
sudo reboot
```

---

## Verify the architecture

Run:

```bash
uname -m
```

Apple Silicon should normally show:

```text
aarch64
```

Intel should normally show:

```text
x86_64
```

---

## Check the network

Run:

```bash
ip addr
```

Test external connectivity:

```bash
curl https://example.com
```

You can also inspect routing:

```bash
ip route
```

---

# 9. Create the Windows 11 VM

Return to the main UTM window.

Click:

```text
+
```

Select:

```text
Create a New Virtual Machine
```

---

## Step 1 — Select Virtualize

Choose:

```text
Virtualize
```

---

## Step 2 — Select Windows

Choose:

```text
Windows
```

---

## Step 3 — Select Windows ISO

Browse to your ISO.

### Apple Silicon

Use:

```text
Windows 11 ARM64
```

### Intel

Use:

```text
Windows 11 x64
```

Enable:

```text
Install Windows 10 or higher
```

Also enable:

```text
Install drivers and SPICE tools
```

if UTM displays the option.

---

## Step 4 — Allocate RAM

Recommended:

```text
8192 MB
```

Minimum practical lab configuration:

```text
4096 MB
```

If the Mac only has 8 GB of physical RAM, avoid allocating 8 GB to Windows.

---

## Step 5 — Allocate CPU

Recommended:

```text
4 CPU cores
```

At least two cores should be assigned to Windows 11.

---

## Step 6 — Create virtual disk

Recommended:

```text
80 GB
```

A 64 GB disk may be sufficient for basic experiments.

For development or security labs:

```text
100 GB
```

provides more flexibility.

---

## Step 7 — Shared directory

Skip shared folders initially if the Windows VM will be used for cybersecurity testing.

They can be enabled later if required.

---

## Step 8 — Name the VM

Example:

```text
Windows-11-Lab
```

Click:

```text
Save
```

---

# 10. Install Windows 11

Start:

```text
Windows-11-Lab
```

When prompted:

```text
Press any key to boot from CD or DVD
```

press a key.

The Windows installer should appear.

---

## Step 1 — Select language

Choose the desired:

```text
Language
Time format
Keyboard layout
```

Click:

```text
Next
```

---

## Step 2 — Start installation

Select:

```text
Install now
```

---

## Step 3 — Product key

If you already have a Windows product key, enter it.

Otherwise, Windows Setup may provide:

```text
I don't have a product key
```

Activation can be completed later with a valid license.

---

## Step 4 — Select edition

Choose the edition corresponding to your license.

Example:

```text
Windows 11 Pro
```

---

## Step 5 — Accept license terms

Accept Microsoft's license terms and continue.

---

## Step 6 — Installation type

Choose:

```text
Custom: Install Windows only
```

---

## Step 7 — Select virtual disk

You should see the UTM virtual drive.

Select:

```text
Drive 0 Unallocated Space
```

Click:

```text
Next
```

Windows will create the required partitions automatically.

---

## Step 8 — Wait for installation

Windows will:

```text
Copy files
Install features
Install updates
Restart
```

The VM may restart several times.

Do not boot from the installer again after Windows has been installed.

---

# 11. Install Windows Guest Tools

UTM Guest Tools provide functionality such as:

- Improved display drivers
- Network drivers
- Clipboard support
- Mouse integration
- Shared-directory support

UTM may install these automatically when the Windows VM is created.

If not, start Windows and use the UTM toolbar to select:

```text
CD/DVD → Install Windows Guest Tools
```

Inside Windows, open:

```text
File Explorer
```

Find the mounted UTM CD.

Run the SPICE guest-tools installer.

It will normally have a filename similar to:

```text
spice-guest-tools-<version>.exe
```

Complete the installation.

Restart Windows.

---

# 12. Configure VM Networking

UTM normally uses shared/NAT networking by default.

The architecture becomes:

```text
                     Internet
                         |
                      macOS
                         |
                  UTM NAT Network
                    /        \
                   /          \
             Kali VM       Windows VM
```

This configuration allows the VMs to access the Internet without directly exposing them to the physical network.

---

## Kali network information

Run:

```bash
ip addr
```

Look for an interface such as:

```text
eth0
```

or:

```text
enp0s1
```

You should see an IP address.

---

## Windows network information

Open PowerShell:

```powershell
ipconfig
```

Look for:

```text
IPv4 Address
Default Gateway
DNS Servers
```

---

# 13. Test Communication Between the VMs

Virtual-machine networking behavior depends on the selected UTM network mode.

First determine the IP addresses.

---

## Kali

```bash
ip addr
```

Example:

```text
192.168.x.x
```

---

## Windows

Open PowerShell:

```powershell
ipconfig
```

Example:

```text
192.168.x.x
```

---

## Test Windows from Kali

From Kali:

```bash
ping <WINDOWS-IP>
```

Example:

```bash
ping 192.168.64.10
```

Windows Firewall may block ICMP echo requests by default.

A failed ping therefore does not automatically mean networking is broken.

---

## Test Kali services

A better laboratory test is to start a temporary web server on Kali.

```bash
python3 -m http.server 8080
```

Determine Kali's IP:

```bash
hostname -I
```

Then open a browser inside Windows and visit:

```text
http://<KALI-IP>:8080
```

For example:

```text
http://192.168.64.5:8080
```

If the Python directory listing appears, communication between the VMs is working.

Stop the web server with:

```text
CTRL+C
```

---

# 14. Create Snapshots or Backups

Before performing experiments that could damage a VM, create a backup or clone.

Useful checkpoints include:

```text
01-Fresh-Install
02-Updated
03-Guest-Tools-Installed
04-Lab-Ready
```

For example:

```text
Kali-Linux-Lab
└── 04-Lab-Ready

Windows-11-Lab
└── 04-Lab-Ready
```

A clean restore point is extremely useful for cybersecurity labs because systems can quickly become misconfigured or compromised during experiments.

---

# 15. Recommended Lab Configuration

A good final environment for a Mac with at least 16 GB of RAM is:

## Kali

```text
Name:       Kali-Linux-Lab
CPU:        4 cores
RAM:        4 GB
Disk:       60 GB
Network:    NAT / Shared
```

## Windows

```text
Name:       Windows-11-Lab
CPU:        4 cores
RAM:        6-8 GB
Disk:       80 GB
Network:    NAT / Shared
```

Avoid running both VMs simultaneously if doing so causes heavy memory pressure on the Mac.

---

# 16. Troubleshooting

## Kali VM does not boot

Verify that the correct architecture was downloaded.

### Apple Silicon

Correct:

```text
ARM64
```

Incorrect for native virtualization:

```text
AMD64
```

### Intel

Correct:

```text
AMD64
```

---

## Windows installer will not boot

Verify the architecture.

### Apple Silicon

Use:

```text
Windows 11 ARM64
```

### Intel

Use:

```text
Windows 11 x64
```

---

## UTM boots into an EFI shell

Check that:

1. The ISO is mounted.
2. The ISO architecture matches the VM.
3. The VM boot order includes the virtual CD/DVD.
4. You pressed a key when Windows displayed:

```text
Press any key to boot from CD or DVD
```

---

## Windows has no Internet connection

Install the UTM/SPICE Guest Tools.

From UTM:

```text
CD/DVD
    ↓
Install Windows Guest Tools
```

Then run the installer from Windows.

Restart the VM afterward.

---

## Windows setup requires a network connection

If the network driver is unavailable during Windows Setup, try installing the UTM Windows drivers through the guest-tools media.

New Windows releases may change the available offline-account workflow, so prefer installing the network drivers and completing normal Windows Setup.

---

## Kali clipboard does not work

Install:

```bash
sudo apt update
sudo apt install spice-vdagent -y
```

Then reboot:

```bash
sudo reboot
```

---

## Kali screen resolution is poor

Install the SPICE agent:

```bash
sudo apt install spice-vdagent -y
```

Reboot the VM.

Also check the VM's display configuration in UTM.

---

## VM performance is very slow

Check whether you selected:

```text
Virtualize
```

rather than:

```text
Emulate
```

when using matching architectures.

For example:

```text
M-series Mac
        +
ARM64 operating system
        +
Virtualize
        =
Best performance
```

Running x86_64 operating systems through CPU emulation on Apple Silicon will usually be considerably slower.

---

## Mac becomes slow

Reduce VM RAM or CPU allocation.

Check memory pressure using:

```text
Applications
    ↓
Utilities
    ↓
Activity Monitor
    ↓
Memory
```

You can also run:

```bash
top
```

from macOS Terminal.

---

# 17. Security Considerations

Virtual machines should still be treated as real computers.

## Keep systems updated

### Kali

```bash
sudo apt update
sudo apt full-upgrade -y
```

### Windows

Use:

```text
Settings
    ↓
Windows Update
    ↓
Check for updates
```

---

## Use isolated networking for security experiments

When experimenting with malware, vulnerable machines, exploitation, or intentionally insecure software, do not automatically connect those systems to your normal LAN.

Prefer an isolated or host-only laboratory network where possible.

An advanced lab can use:

```text
                    macOS
                      |
                     UTM
                      |
              Isolated Lab Network
                /            \
               /              \
        Kali Attacker      Windows Target
```

---

## Disable unnecessary shared folders

Shared folders can create an additional path between the VM and the host.

For high-risk experiments, avoid:

```text
Host folder sharing
Shared clipboard
Drag and drop
Unnecessary USB passthrough
```

---

## Do not test systems without authorization

Kali Linux contains offensive-security tools.

Only use them against:

- Systems you own
- Systems specifically created for your lab
- CTF environments
- Training platforms
- Systems where you have explicit authorization

---

# 18. Validation Checklist

The project is complete when all of the following are true:

```text
[ ] UTM installed successfully
[ ] Correct Mac CPU architecture identified

Kali:
[ ] Correct Kali ISO downloaded
[ ] Kali VM created
[ ] Kali installed
[ ] Kali updated
[ ] spice-vdagent installed
[ ] Internet connectivity working

Windows:
[ ] Correct Windows ISO downloaded
[ ] Windows VM created
[ ] Windows installed
[ ] Guest tools installed
[ ] Network working
[ ] Windows Update working

Lab:
[ ] Kali IP address identified
[ ] Windows IP address identified
[ ] VM networking tested
[ ] Clean VM backups/checkpoints created
```

---

# 19. Project Result

The final environment should look similar to:

```text
macOS
│
└── UTM
    │
    ├── Kali-Linux-Lab
    │   ├── Linux
    │   ├── Security tools
    │   ├── SPICE agent
    │   └── Virtual network adapter
    │
    └── Windows-11-Lab
        ├── Windows 11
        ├── UTM Guest Tools
        ├── SPICE drivers
        └── Virtual network adapter
```

You now have a reusable local virtualization environment containing both Linux and Windows.

This environment can be extended into a larger cybersecurity lab by adding systems such as:

```text
Ubuntu Server
Metasploitable
OWASP Juice Shop
Security Onion
Active Directory Domain Controller
Windows Server
SIEM platforms
Vulnerable web applications
Additional Windows clients
```

A future multi-machine lab could look like:

```text
                         macOS
                           |
                          UTM
                           |
                    Virtual Lab Network
                           |
        +------------------+-------------------+
        |                  |                   |
     Kali Linux       Windows 11        Windows Server
     Attacker         Workstation      Domain Controller
        |
        +--------------------------------------+
                           |
                   Vulnerable Servers
```

---

# Useful Commands

## macOS

Check architecture:

```bash
uname -m
```

Check system information:

```bash
system_profiler SPHardwareDataType
```

---

## Kali

Update package lists:

```bash
sudo apt update
```

Upgrade:

```bash
sudo apt full-upgrade -y
```

Install UTM integration:

```bash
sudo apt install spice-vdagent qemu-guest-agent -y
```

Show IP addresses:

```bash
ip addr
```

Show route:

```bash
ip route
```

Show compact IP information:

```bash
hostname -I
```

Temporary HTTP server:

```bash
python3 -m http.server 8080
```

---

## Windows PowerShell

Display network configuration:

```powershell
ipconfig
```

Detailed network configuration:

```powershell
ipconfig /all
```

Test a TCP connection:

```powershell
Test-NetConnection <IP> -Port 8080
```

Example:

```powershell
Test-NetConnection 192.168.64.5 -Port 8080
```

---

# Conclusion

This project demonstrated how to:

1. Determine the CPU architecture of a Mac.
2. Install and configure UTM.
3. Create a Kali Linux virtual machine.
4. Create a Windows 11 virtual machine.
5. Install guest integration tools.
6. Configure virtual networking.
7. Test communication between virtual machines.
8. Establish restore points for future lab exercises.

The environment provides a foundation for building more advanced virtualization, networking, system-administration, and cybersecurity projects.
