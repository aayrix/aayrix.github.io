+++
date = '2026-06-02T10:00:00+05:00'
title = 'How to Install Arch Linux — Complete Step-by-Step Guide'
description = 'A complete Arch Linux installation guide: download ISO, partition disks, install base system, configure bootloader, and set up a desktop environment.'
tags = ['arch-linux', 'linux', 'installation', 'guide', 'terminal']
categories = ['Linux', 'Tutorials']
ShowToc = true
ShowReadingTime = true

[cover]
image = "/images/covers/arch-cover.png"
alt = "Arch Linux installation guide"
caption = "Arch Linux — Complete Installation Guide"
+++

<div class="post-logo single">

![Arch Linux Logo](/images/logos/archlinux.svg)

</div>

Arch Linux is a lightweight, rolling-release distribution that gives you full control over your system. This guide walks through a **UEFI + GPT** installation with **systemd-boot** and a minimal setup you can extend later.

> **Prerequisites:** A 64-bit PC with UEFI, internet access, and a USB drive (4 GB+). Back up all data on the target disk before proceeding.

## Table of Contents

1. [Download the ISO](#1-download-the-iso)
2. [Create a Bootable USB](#2-create-a-bootable-usb)
3. [Boot into the Live Environment](#3-boot-into-the-live-environment)
4. [Connect to the Internet](#4-connect-to-the-internet)
5. [Partition the Disk](#5-partition-the-disk)
6. [Format and Mount Partitions](#6-format-and-mount-partitions)
7. [Install Base Packages](#7-install-base-packages)
8. [Configure the System](#8-configure-the-system)
9. [Install Bootloader](#9-install-bootloader)
10. [Reboot and First Login](#10-reboot-and-first-login)
11. [Post-Install Essentials](#11-post-install-essentials)

---

## 1. Download the ISO

Download the latest Arch Linux ISO from the official site:

```bash
# On any Linux machine — verify checksum after download
wget https://geo.mirror.pkgbuild.com/iso/latest/archlinux-x86_64.iso
wget https://geo.mirror.pkgbuild.com/iso/latest/sha256sums.txt
sha256sum -c sha256sums.txt
```

Or download from: [archlinux.org/download](https://archlinux.org/download/)

---

## 2. Create a Bootable USB

Replace `/dev/sdX` with your USB device (use `lsblk` to identify it).

```bash
# List block devices
lsblk

# Write ISO to USB (DESTROYS all data on the USB)
sudo dd bs=4M if=archlinux-x86_64.iso of=/dev/sdX status=progress oflag=sync
```

**Windows alternative:** Use [Rufus](https://rufus.ie/) or [balenaEtcher](https://etcher.balena.io/).

---

## 3. Boot into the Live Environment

1. Insert the USB and reboot.
2. Enter your BIOS/UEFI boot menu (usually `F12`, `F2`, or `Del`).
3. Select the USB drive.
4. Choose **Arch Linux install medium (x86_64, UEFI)** from the GRUB menu.

You should land at a root shell prompt.

---

## 4. Connect to the Internet

### Wi-Fi

```bash
# Start interactive Wi-Fi setup
iwctl

# Inside iwctl:
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect "YourWiFiName"
exit

# Verify connection
ping -c 3 archlinux.org
```

### Ethernet

Plug in the cable — DHCP should work automatically.

```bash
ping -c 3 archlinux.org
```

### Enable NTP (recommended)

```bash
timedatectl set-ntp true
timedatectl status
```

---

## 5. Partition the Disk

List disks and identify your target (e.g. `/dev/nvme0n1` or `/dev/sda`):

```bash
lsblk
fdisk -l
```

Example layout for a **512 GB NVMe** with dual boot in mind:

| Partition | Size | Type | Mount |
|-----------|------|------|-------|
| `/dev/nvme0n1p1` | 512 MB | EFI System | `/boot` |
| `/dev/nvme0n1p2` | 100 GB | Linux root | `/` |
| `/dev/nvme0n1p3` | Remaining | Linux home | `/home` |
| swap | 8–16 GB | Linux swap | — |

Use `fdisk` or `cfdisk`:

```bash
cfdisk /dev/nvme0n1
```

In `cfdisk`:
- Select **GPT** label
- Create **EFI System** partition (512 MB)
- Create **Linux filesystem** for root
- Create **Linux filesystem** for home (optional)
- Create **Linux swap** (optional)
- Write changes and quit

---

## 6. Format and Mount Partitions

Adjust partition names to match your system:

```bash
# Format EFI partition
mkfs.fat -F32 /dev/nvme0n1p1

# Format root (ext4)
mkfs.ext4 /dev/nvme0n1p2

# Format home (ext4) — optional
mkfs.ext4 /dev/nvme0n1p3

# Enable swap — optional
mkswap /dev/nvme0n1p4
swapon /dev/nvme0n1p4

# Mount partitions
mount /dev/nvme0n1p2 /mnt

# Create and mount boot
mkdir -p /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot

# Create and mount home — optional
mkdir -p /mnt/home
mount /dev/nvme0n1p3 /mnt/home
```

---

## 7. Install Base Packages

```bash
# Update mirror list (optional but recommended)
pacman -Sy reflector
reflector --country 'United States' --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist

# Install base system + firmware + editor
pacstrap -K /mnt base linux linux-firmware nano vim networkmanager

# Generate fstab
genfstab -U /mnt >> /mnt/fstab

# Verify fstab
cat /mnt/fstab
```

---

## 8. Configure the System

```bash
# Chroot into new system
arch-chroot /mnt

# Set timezone (replace with your region)
ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
hwclock --systohc

# Set locale
echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf

# Set hostname
echo "archlinux" > /etc/hostname

# Configure hosts file
cat >> /etc/hosts << EOF
127.0.0.1   localhost
::1         localhost
127.0.1.1   archlinux.localdomain archlinux
EOF

# Set root password
passwd root

# Create a regular user
useradd -m -G wheel,audio,video,optical,storage -s /bin/bash aayrix
passwd aayrix

# Allow wheel group to use sudo
EDITOR=nano visudo
# Uncomment: %wheel ALL=(ALL:ALL) ALL

# Enable NetworkManager
systemctl enable NetworkManager
```

---

## 9. Install Bootloader

```bash
# Still inside arch-chroot

# Install systemd-boot
bootctl install

# Create boot entry
mkdir -p /boot/loader/entries
cat > /boot/loader/loader.conf << EOF
default arch.conf
timeout 3
editor 0
EOF

# Find your root partition UUID
blkid

# Create entry (replace UUID with your root partition UUID)
cat > /boot/loader/entries/arch.conf << EOF
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options root=UUID=YOUR-ROOT-UUID rw
EOF

# Exit chroot
exit
```

---

## 10. Reboot and First Login

```bash
# Unmount all partitions
umount -R /mnt

# Reboot
reboot
```

Remove the USB when prompted. Log in as your user (`aayrix`) or root.

---

## 11. Post-Install Essentials

After first boot, log in and run:

```bash
# Connect to Wi-Fi (if not already connected)
nmcli device wifi list
nmcli device wifi connect "YourWiFiName" password "YourPassword"

# Update the entire system
sudo pacman -Syu

# Install essential tools
sudo pacman -S git base-devel curl wget htop

# Install a desktop environment (example: KDE Plasma)
sudo pacman -S plasma kde-applications sddm
sudo systemctl enable sddm
sudo reboot
```

### Install yay (AUR helper) — optional

```bash
cd /tmp
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Can't boot after install | Reboot into live USB, `arch-chroot /mnt`, check `/boot/loader/entries/arch.conf` UUID |
| No Wi-Fi in live environment | Use `iwctl` or plug in ethernet |
| `pacstrap` fails | Check internet with `ping archlinux.org`, try a different mirror |
| Black screen after login | Install GPU drivers: `sudo pacman -S nvidia` or `mesa` |

---

## Useful Resources

- [Arch Wiki — Installation Guide](https://wiki.archlinux.org/title/Installation_guide)
- [Arch Wiki — General Recommendations](https://wiki.archlinux.org/title/General_recommendations)
- My GitHub: [github.com/aayrix](https://github.com/aayrix)

Arch Linux rewards patience. Take it one step at a time, read the [Arch Wiki](https://wiki.archlinux.org/) when stuck, and you'll have a system tailored exactly to your needs.
