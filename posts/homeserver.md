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

# Building a Home Server with an Old Headless Laptop Running Debian - Pt 1

I already had a raspberry-pi homes server but it stopped working due to moisture damage.  so I'm using my old lap as my new server, it's very essential to me these days...
I'm writing this blog as a guide and as a future reference for me and anyone who want to do the same :)

I'm writing this in two parts, this part 1 contains how to install and setup debain for a headless server setup.

I personally use this server for local streaming with plex/jellyfin, cloud storage,  IoT applications, personal VPN with wireguard, ad blocking with pi hole and many other...

> This guide works for both laptops and desktops. Power optimization and other small differences are noted separately.

## Prerequisites

* Laptop (at least dual-core CPU & 4GB RAM recommended, also works on lower ram size too xD)
* USB drive (8GB minimum, its fine as long as debian iso fits in.)
* Ethernet cable (for initial setup or maybe permanent )
* [Debian 12 Netinst ISO](https://www.debian.org)

these were my system specs :

```bash
abhiakl@skittles:~$ neofetch
       _,met$$$$$gg.          abhiak@skittlesjr
    ,g$$$$$$$$$$$$$$$P.       ----------------
  ,g$$P"     """Y$$.".        OS: Debian GNU/Linux 12 (bookworm) x86_64
 ,$$P'              `$$$.     Host: INVA Lenovo Ideapad *****
',$$P       ,ggs.     `$$b:   Kernel: 6.1.0-37-amd64
`d$$'     ,$P"'   .    $$$    Uptime: 1 hour, 28 mins
 $$P      d$'     ,    $$P    Packages: 785 (dpkg)
 $$:      $$.   -    ,d$$'    Shell: bash 5.2.15
 $$;      Y$b._   _,d$P'      Terminal: /dev/pts/1
 Y$$.    `.`"Y$$$$P"'         CPU: Intel i5-8250U (8) @ 3.400GHz
 `$$b      "-.__              GPU: Intel UHD Graphics 620
  `Y$$                        GPU: AMD ATI Radeon R7 M260/M265 / M340/M360 / M440/M445 / 530/535 / 620/625 Mobile
   `Y$$.                      Memory: 478MiB / 11868MiB
     `$$b.
       `Y$$b.
          `"Y$b._
              `"""
```

***

## Step 1: Prepare the Laptop

### Hardware Preparation

1. Remove battery (if swollen) and run on AC power or replace a new battery maybe, in my case my battery was damaged so i removed it
2. Test RAM and storage with \`memtest86+\`
3. Clean internal fans and heatsinks

thes steps are purely optional

### Create Boot Media

```bash
# Linux/macOS
dd if=debian-12.x.x-amd64-netinst.iso of=/dev/sdX bs=4M status=progress
# replace sdX with target storage and .iso with target file name
# For Windows (use Rufus or Etcher)
```

***

## Step 2: Install Debian (Headless Mode)

Boot from USB and install with these key settings:

* Key settings : choose the suitable language and region
  * hostname : this will be the name of your computer ( I'm naming skittlesjr a random name )
  * domain name : leave it blank or use something like `local` if you're not sure or not going to use, it's basically the DNS domain your system belongs to on the network, meaning you can use skittlesjr.local instead of using 192.168.1.70 to access ssh or something different in your local network.
  * setup root password, username (i use abhiak) and your user password too...
* Partitioning: you can either use entire disk (LVM recommended) or go for the manual partition just like i did:

```plaintext
120 GB Disk
├── /boot/efi      (FAT32, 512 MB)      # Required for UEFI systems
├── swap           (swap, 4 GB)          # Adjust based on RAM/hibernation needs
└── /              (ext4, ~115.5 GB)    # Root partition including /boot and /home inside it
```

I didnt create a swap partition at this point, I went for the page file later after installing the system with :

```bash
~$ sudo fallocate -l 2G /swapfile
~$ sudo chmod 600 /swapfile
~$ sudo mkswap /swapfile
~$ sudo swapon /swapfile
# to make it permanent : 
~$ echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
#check the swap with :
~$ free -h
```

* Software Selection:
  * ☐ Desktop environment (UNCHECK)
  * ☐ web server (can check if u want, this installs apache, I'm installing ngrok later on)
  * ☑️ SSH server (this is kinda essential for remote access)
  * ☑️ standard system utilities

#### Post-installation check

```bash
hostnamectl
# Should show: Chassis: laptop | Virtualization: none
```

> In some cases, installation issues may cause basic commands like sudo, poweroff, or reboot to be unavailable to fix that, switch to root and run the following command to install all that :

```bash
~$ apt update
~$ apt install sudo systemd-sysv util-linux isc-dhcp-client net-tools iproute2 rfkill -y

```

***

## Step 3: Configure Headless Operation

### Disable Unnecessary Services

```bash
sudo systemctl mask bluetooth.service
sudo systemctl mask wpa_supplicant.service
#the second command is for disabling wifi, if you're planning to connect via 
#wifi, avoid this command, in my case i'm using my Gigabit switch which is 
#faster than the 300 mbps 5G wifi link
```

### Configure Power Settings

##### if you're configuring on laptop

```bash
sudo apt install laptop-mode-tools
sudo nano /etc/laptop-mode/laptop-mode.conf
```

Set:

`ENABLE\_LAPTOP\_MODE\_ON\_BATTERY=1`
`ENABLE\_LAPTOP\_MODE\_ON\_AC=1`
`ENABLE\_LAPTOP\_MODE\_WHEN\_LID\_CLOSED=1`

##### if you're configuring on Desktop
