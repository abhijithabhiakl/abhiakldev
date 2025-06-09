---
title: Setting up a home server
slug: homeserver
description: Setting up a home server with my old laptop lying around
tags:
  - technical
  - learning
  - personal
added: 2025-06-09T03:57:40.984Z
---

\# Building a Home Server with an Old Headless Laptop Running Debian

!\[Home Server Illustration]\([https://example.com/path/to/server-image.jpg](https://example.com/path/to/server-image.jpg)) \*Replace with your own image\*

Transform that dusty old laptop into a powerful home server! This guide walks you through creating a headless Debian server for self-hosting applications, file sharing, media streaming, and more.

\## Why Use an Old Laptop?
\- \*\*Eco-Friendly\*\*: Repurpose hardware instead of recycling
\- \*\*Power-Efficient\*\*: Laptops consume less power than desktops
\- \*\*Built-in UPS\*\*: Battery backup during power outages
\- \*\*Compact Size\*\*: Fits anywhere in your home

\## Prerequisites
\- Laptop (at least dual-core CPU & 4GB RAM recommended)
\- USB drive (8GB minimum)
\- Ethernet cable (for initial setup)
\- \[Debian 12 Netinst ISO]\([https://www.debian.org/download](https://www.debian.org/download))

\---

\## Step 1: Prepare the Laptop
\### Hardware Preparation
1\. Remove battery (if swollen) and run on AC power
2\. Test RAM and storage with \`memtest86+\`
3\. Clean internal fans and heatsinks

\### Create Boot Media
\`\`\`bash
\# Linux/macOS
dd if=debian-12.x.x-amd64-netinst.iso of=/dev/sdX bs=4M status=progress

\# Windows (use Rufus or Etcher)

***

## Step 2: Install Debian (Headless Mode)

Boot from USB and install with these key settings:

* Partitioning: Use entire disk (LVM recommended)
* Software Selection:
* ☑️ SSH server
* ☐ Desktop environment (UNCHECK)
* ☐ Print server (UNCHECK)
* User Setup: Create non-root user with sudo privileges

bashCopyDownload# Post-installation check
hostnamectl
\# Should show: Chassis: laptop | Virtualization: none

***

## Step 3: Configure Headless Operation

### Enable Automatic Login to Console (For recovery)

bashCopyDownloadsudo systemctl edit getty\@tty1

Add:

textCopyDownload\[Service]
ExecStart=
ExecStart=-/sbin/agetty --autologin yourusername --noclear %I $TERM

### Disable Unnecessary Services

bashCopyDownloadsudo systemctl mask bluetooth.service
sudo systemctl mask wpa\_supplicant.service

### Configure Power Settings

bashCopyDownloadsudo apt install laptop-mode-tools
sudo nano /etc/laptop-mode/laptop-mode.conf

Set:

textCopyDownloadENABLE\_LAPTOP\_MODE\_ON\_BATTERY=1
ENABLE\_LAPTOP\_MODE\_ON\_AC=1
ENABLE\_LAPTOP\_MODE\_WHEN\_LID\_CLOSED=1

***

## Step 4: Network Configuration

### Set Static IP

bashCopyDownloadsudo nano /etc/network/interfaces

Replace with:

textCopyDownloadauto enp0s25
iface enp0s25 inet static
address 192.168.1.100/24
gateway 192.168.1.1
dns-nameservers 192.168.1.1 1.1.1.1

### Install Persistent SSH

bashCopyDownloadsudo systemctl enable ssh
sudo nano /etc/ssh/sshd\_config

Modify:

textCopyDownloadPermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes

***

## Step 5: Essential Services Setup

### Firewall Configuration (UFW)

bashCopyDownloadsudo apt install ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw enable

### Automatic Updates

bashCopyDownloadsudo apt install unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades

### Storage Management

bashCopyDownloadsudo apt install cockpit
sudo systemctl enable --now cockpit.socket

Access via: https\://your-server-ip:9090

***

## Step 6: Install Your First Services

### Docker Setup

bashCopyDownloadsudo apt install docker.io docker-compose
sudo usermod -aG docker $USER

### Sample Docker Compose (Portainer)

yamlCopyDownloadversion: '3'
services:
portainer:
image: portainer/portainer-ce:latest
container\_name: portainer
restart: unless-stopped
ports:
\- "9000:9000"
volumes:
\- /var/run/docker.sock:/var/run/docker.sock
\- portainer\_data:/data
volumes:
portainer\_data:

***

## Recommended Services

| Service   | Use Case           | Install Method                                                               |
| --------- | ------------------ | ---------------------------------------------------------------------------- |
| Samba     | File Sharing       | sudo apt install samba                                                       |
| Plex      | Media Server       | [Docker](https://hub.docker.com/r/plexinc/pms-docker)                        |
| Nextcloud | Cloud Storage      | snap install nextcloud                                                       |
| Pi-hole   | Network Ad-Blocker | curl -sSL [https://install.pi-hole.net](https://install.pi-hole.net) \| bash |

***

## Power Management Tips

1. Disable GUI Boot:bashCopyDownloadsudo systemctl set-default multi-user.target
2. Reduce CPU Frequency:bashCopyDownloadsudo apt install cpufrequtils
   echo 'GOVERNOR="powersave"' | sudo tee /etc/default/cpufrequtils
3. Monitor Power Usage:bashCopyDownloadsudo apt install powertop
   sudo powertop --auto-tune
