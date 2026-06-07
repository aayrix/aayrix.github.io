+++
date = '2026-06-06T10:00:00+05:00'
title = 'How to Install Ubuntu — Complete Step-by-Step Guide'
description = 'A complete Ubuntu installation guide: download ISO, create a bootable USB, run the installer, partition your disk, and set up your system after first boot.'
tags = ['ubuntu', 'linux', 'installation', 'guide', 'terminal']
categories = ['Linux', 'Tutorials']
ShowToc = true
ShowReadingTime = true

[cover]
image = "/images/covers/ubuntu-cover.png"
alt = "Ubuntu Linux installation guide"
caption = "Ubuntu 24.04 LTS — Complete Installation Guide"
+++

<div class="post-logo single">

![Ubuntu Logo](/images/logos/ubuntu.svg)

</div>

Ubuntu is one of the most popular Linux distributions — beginner-friendly, well-supported, and great for desktops, laptops, and servers. This guide walks through installing **Ubuntu 24.04 LTS** (or 22.04 LTS) on a UEFI system using the graphical installer.

> **Prerequisites:** A 64-bit PC with UEFI, 4 GB+ RAM, 25 GB+ free disk space, internet access, and a USB drive (8 GB+). Back up all data before proceeding.

## Table of Contents

1. [Download the ISO](#1-download-the-iso)
2. [Create a Bootable USB](#2-create-a-bootable-usb)
3. [Boot from USB](#3-boot-from-usb)
4. [Start the Installer](#4-start-the-installer)
5. [Configure Installation Options](#5-configure-installation-options)
6. [Partition Your Disk](#6-partition-your-disk)
7. [Create Your User Account](#7-create-your-user-account)
8. [Finish Installation](#8-finish-installation)
9. [Post-Install Essentials](#9-post-install-essentials)
10. [Troubleshooting](#10-troubleshooting)

---

## 1. Download the ISO

Download the latest **Ubuntu Desktop LTS** ISO from the official site:

**[ubuntu.com/download/desktop](https://ubuntu.com/download/desktop)**

Recommended version: **Ubuntu 24.04 LTS** (supported until 2029).

### Verify the download (Linux)

```bash
# Download ISO and checksums
wget https://releases.ubuntu.com/24.04/ubuntu-24.04-desktop-amd64.iso
wget https://releases.ubuntu.com/24.04/SHA256SUMS

# Verify checksum
sha256sum -c SHA256SUMS 2>&1 | grep ubuntu-24.04-desktop-amd64.iso
```

---

## 2. Create a Bootable USB

Replace `/dev/sdX` with your USB device. Use `lsblk` to find the correct drive — **do not** pick your main system disk.

### Linux (dd)

```bash
# List block devices
lsblk

# Write ISO to USB (DESTROYS all data on the USB)
sudo dd bs=4M if=ubuntu-24.04-desktop-amd64.iso of=/dev/sdX status=progress oflag=sync

# Safely eject
sudo eject /dev/sdX
```

### Linux (Ventoy — reusable USB)

```bash
# Install Ventoy, then copy the ISO file to the USB — no re-flashing needed per ISO
# Download from: https://www.ventoy.net/en/download.html
sudo bash Ventoy2Disk.sh -i /dev/sdX
cp ubuntu-24.04-desktop-amd64.iso /media/$USER/Ventoy/
```

### Windows

- **[Rufus](https://rufus.ie/)** — Select ISO, partition scheme **GPT**, target system **UEFI**
- **[balenaEtcher](https://etcher.balena.io/)** — Simple click-and-flash tool

### macOS

```bash
# List disks
diskutil list

# Unmount USB (replace diskN)
diskutil unmountDisk /dev/diskN

# Write ISO
sudo dd if=ubuntu-24.04-desktop-amd64.iso of=/dev/rdiskN bs=4m status=progress
```

---

## 3. Boot from USB

1. Insert the USB drive.
2. Restart your computer.
3. Open the **boot menu** (common keys: `F12`, `F2`, `Esc`, `Del`, `F10`).
4. Select the USB drive (often labeled with the USB brand name).
5. Choose **Try or Install Ubuntu** from the GRUB menu.

If the system boots into Windows instead, disable **Fast Startup** in Windows Power Options, or change boot order in BIOS/UEFI settings.

---

## 4. Start the Installer

On the live desktop:

1. Wait for the desktop to load (this may take a minute).
2. Double-click **Install Ubuntu 24.04 LTS** on the desktop.
   - Or click **Install Ubuntu** in the dock.
3. Select your **language** and click **Continue**.

### Keyboard layout

Choose your keyboard layout (e.g. English (US)) and click **Continue**.

---

## 5. Configure Installation Options

### Updates and other software

Select:

- **Normal installation** — includes web browser, office tools, media players
- **Minimal installation** — browser and basic utilities only (lighter install)

Check these boxes if applicable:

- [x] **Download updates while installing Ubuntu** (requires internet)
- [x] **Install third-party software** (Wi-Fi drivers, NVIDIA, media codecs)

Click **Continue**.

### Installation type

Ubuntu detects existing operating systems and offers options:

| Option | When to use |
|--------|-------------|
| **Install Ubuntu alongside Windows** | Dual boot — Ubuntu shrinks Windows partition automatically |
| **Erase disk and install Ubuntu** | Dedicated machine — wipes entire disk |
| **Something else** | Manual partitioning — full control |

For most users with Windows already installed, choose **Install Ubuntu alongside Windows**.

For a clean dedicated install, choose **Erase disk and install Ubuntu**.

Click **Install Now** → **Continue** to confirm partition changes.

---

## 6. Partition Your Disk

### Automatic (recommended for beginners)

Let Ubuntu handle partitioning:

- **Alongside Windows** — creates `/` (root) and swap automatically
- **Erase disk** — uses the full disk with a single `/` partition

### Manual partitioning (Something else)

For advanced users, create these partitions on a **GPT** disk:

| Mount | Type | Size | Notes |
|-------|------|------|-------|
| `/boot/efi` | EFI System Partition | 512 MB | Required for UEFI |
| `/` | ext4 | 50–100 GB | Root filesystem |
| `/home` | ext4 | Remaining space | User files (survives reinstalls) |
| swap | swap area | RAM size (4–16 GB) | Optional with 16 GB+ RAM |

Steps in the manual partitioner:

1. Select free space → **Add**
2. Create each partition with the sizes above
3. Set **Use as** to the correct filesystem type
4. Set **Mount point** for each partition
5. Click **Install Now**

---

## 7. Create Your User Account

Fill in the form:

```
Your name:     Aayrix
Computer name: aayrix-pc
Username:      aayrix
Password:      ********
```

Options:

- **Require my password to log in** — recommended
- **Log in automatically** — convenient but less secure
- **Use Active Directory** — for enterprise networks only

Click **Continue**.

---

## 8. Finish Installation

Ubuntu copies files and installs the system (10–30 minutes depending on hardware and internet speed).

When you see **Installation Complete**:

1. Click **Restart Now**
2. Remove the USB when prompted
3. Press **Enter**
4. Log in with your username and password

---

## 9. Post-Install Essentials

Open **Terminal** (`Ctrl + Alt + T`) and run these commands:

### Update the system

```bash
sudo apt update && sudo apt upgrade -y
```

### Install essential tools

```bash
sudo apt install -y git curl wget htop build-essential vim neofetch
```

### Install proprietary drivers (NVIDIA / Wi-Fi)

```bash
# Open Software & Updates → Additional Drivers
# Or via terminal:
ubuntu-drivers devices
sudo ubuntu-drivers autoinstall
```

### Enable firewall

```bash
sudo ufw enable
sudo ufw status
```

### Install Snap apps (optional)

```bash
sudo snap install code --classic    # VS Code
sudo snap install spotify
sudo snap install discord
```

### Install Flatpak (alternative app store)

```bash
sudo apt install -y flatpak
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

### Set up Git and GitHub

```bash
git config --global user.name "Aayrix"
git config --global user.email "your.email@example.com"

# Generate SSH key for GitHub
ssh-keygen -t ed25519 -C "your.email@example.com"
cat ~/.ssh/id_ed25519.pub
# Add the output to: https://github.com/settings/keys
```

For a full GitHub setup guide, see [GitHub Install & Usage Guide](/posts/github-install-and-usage-guide/).

### Customize your desktop

```bash
# GNOME Tweaks
sudo apt install -y gnome-tweaks

# Change default shell to Zsh (optional)
sudo apt install -y zsh
chsh -s $(which zsh)
```

### Check system info

```bash
neofetch
lsb_release -a
uname -a
```

---

## 10. Troubleshooting

| Problem | Solution |
|---------|----------|
| USB not booting | Disable Secure Boot in BIOS, or enable UEFI boot for USB |
| Black screen after install | Boot with `nomodeset` — at GRUB press `e`, add `nomodeset` to linux line |
| No Wi-Fi after install | Connect via ethernet, run `sudo apt update && sudo ubuntu-drivers autoinstall` |
| Windows dual-boot missing | Run `sudo update-grub` from Ubuntu |
| Installer freezes | Try **Safe Graphics** option at boot menu |
| Slow performance | Install `sudo apt install linux-lowlatency` for audio work, or check swap |

### Boot repair (GRUB broken)

```bash
# Boot from Ubuntu live USB, then:
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt update
sudo apt install -y boot-repair
boot-repair
```

### Reinstall GRUB

```bash
# From live USB terminal
sudo mount /dev/sda2 /mnt          # replace with your root partition
sudo mount /dev/sda1 /mnt/boot/efi # EFI partition
sudo grub-install --boot-directory=/mnt/boot /dev/sda
sudo update-grub --boot-directory=/mnt/boot
```

---

## Ubuntu vs Arch — Which to Choose?

| | Ubuntu | Arch Linux |
|---|--------|------------|
| Difficulty | Beginner-friendly | Advanced |
| Installer | Graphical wizard | Manual terminal |
| Packages | `.deb` / apt | `.pkg` / pacman |
| Release | LTS (5 years) | Rolling release |
| Best for | Daily desktop, servers | Custom minimal systems |

If you want a more hands-on install, see the [Arch Linux Complete Guide](/posts/install-arch-linux-complete-guide/).

---

## Useful Resources

- [Ubuntu Official Documentation](https://help.ubuntu.com/)
- [Ubuntu Community Help Wiki](https://help.ubuntu.com/community/)
- [Ask Ubuntu](https://askubuntu.com/)
- My GitHub: [github.com/aayrix](https://github.com/aayrix)

Ubuntu is a solid choice for your first Linux install or a reliable daily driver. Update regularly, explore the Software app, and enjoy your new system.
