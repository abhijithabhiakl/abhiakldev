---
title: Setting up a home server
slug: homeserver
description: Setting up a home server with an old laptop lying around
tags:
  - technical
  - learning
  - personal
added: 2025-06-09T03:57:40.984Z
---

# Building a Home Server with an Old Headless Laptop Running Debian - Pt 1

I already had a raspberry-pi homeserver but all of a sudden it stopped working due to moisture damage :/  so I'm thinking of using my old lap as my new server, homeserver seems essential to me these days, especially for my Iot projects, So I'm about to build and set up the server-might as well blog the process as go!
I'm writing this blog as a guide and as a future reference for me and anyone who want to do the same

I'm writing this in two parts, this part 1 contains how to install and setup debain for a headless server setup.

I personally use this server for local streaming with plex/jellyfin, cloud storage,  IoT applications, personal VPN with wireguard, ad blocking with pi hole and many other...

> This guide works for both laptops and desktops. Power optimization and other small differences are noted separately.

## Prerequisites

* Laptop (at least dual-core CPU & 4GB RAM recommended, 

       also works on lower ram size too xD, more ram is required to host docker containers)

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
  `Y$$                        GPU: AMD ATI Radeon R7 M260/M265 / 
   `Y$$.                      M340/M360 / M440/M445 / 530/535 / 
     `$$b.                    620/625 Mobile
        Y$$b.                 Memory: 478MiB / 11868MiB
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

```bash
# this would be shown in the terminal as:
abhiakl@skittles:~$
```

* Partitioning: you can either use entire disk (LVM recommended) or go for the manual partition just like i did:

```plaintext
120 GB Disk
├── /boot/efi      (FAT32, 512 MB)      # Required for UEFI systems
├── swap           (swap, 4 GB)          # Adjust based on RAM/hibernation needs
└── /              (ext4, ~115.5 GB)    # Root partition including /boot and /home inside it
```

I didn't create a swap partition at this point since I have a lot ram and I barely have any use for a dedicated swap(such as hibernation), anyway I went for the page file later after installing the system with :

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
  * ☐ web server (can check if you want; this installs apache. I am installing ngrok later on)
  * ☑️ SSH server (this is kinda essential for remote access)
  * ☑️ standard system utilities

#### Post-installation check

```bash
hostnamectl
# Should show: Chassis: laptop | Virtualization: none
```

> In some cases, installation issues may cause basic commands like sudo, poweroff, or reboot to be unavailable to fix that, switch to root and run the following command to install all that :

```bash
~$ su -
~$ apt update
~$ apt install sudo systemd-sysv util-linux isc-dhcp-client net-tools iproute2 rfkill -y

```

***

## Step 3: Network Configuration

Now your server is automatically configured with DHCP either in the server itself or by the router, which changes the server's ip with a set period of time, we don't want that to happen, we want a permanant local ip address to the server, for that we want make the ip static by editing the network configurations

First of all switch to `root` using:

```bash
~$ su -
```

check your network interface with

```bash
~$ ip link 
```

note the interface name you want to configure, usually something like `eth0`, `enp0s3`, `wlp2s0` (WiFi)
now edit interface property with :

```bash
sudo nano /etc/network/interfaces
```

add or replace:

```ini
auto enp2s0
iface enp2s0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4
```

replace the 100 in 192.168.1.100 with your choice

save and exit.
Restart netoworking:

```bash
sudo systemctl restart networking
```

## Step 4: Configure Headless Operation

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

> These settings applies some power saving settings to your server such as reducing cpu frequency, HDD spin down on battery mode, while on AC power it keeps cpu scaling conservative, limits disk power management.
> This settings are optional, for a 24/7 server this reduces heat/power while maintaining stability

##### if you're configuring on Desktop

install powertop

```bash
sudo apt update
sudo apt install powertop -y
```

calibrate powetop

```bash
sudo powertop --calibrate
```

takes a while...
now run

```bash
sudo powertop --auto-tune
```

this will apply all the recommended tunings for power savings (one-time)

TO make tunings persistent across reboots, edit:

```bash
sudo nano /etc/systemd/system/powertop.service
```

paste:

```ini
[Unit]
Description=Powertop tunings
After=multi-user.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/powertop --auto-tune

[Install]
WantedBy=multi-user.target
```

enable it:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable powertop.service
```

check if it is working:

```bash
sudo systemctl status powertop.service
```

## Step 5: Configure firewall

install:

```bash
sudo apt install ufw
```

allow only what you need, here I am allowing some basic ports that I use:

```bash
sudo ufw allow 22        # SSH
sudo ufw allow 1883      # MQTT
sudo ufw allow 3000      # Grafana
sudo ufw allow 8086      # InfluxDB
sudo ufw allow 51820/udp # WireGuard
sudo ufw allow 443       # https
```

enable firewall:

```bash
sudo ufw enable
```

if you add a new service:

```bash
sudo ufw allow <port>
```

to remove:

```bash
sudo ufw delete allow <port>
```

this will show the current status:

```bash
sudo ufw status verbose
```

this is a very basic firewall setup with a very basic uncomplicated simple firewall, you can go advanced levels if u prefer, there are many other advanced firewalls like `nftables`, `firewalld`, `shorewall` etc

I think this is enough setting up for a basic server that runs 24/7 for essential homeserver services.

## Recommended Services

| Service   | Use Case           |
| --------- | ------------------ |
| Samba     | File Sharing       |
| Plex      | Media Server       |
| Nextcloud | Cloud Storage      |
| Pi-hole   | Network Ad-Blocker |

## Final Notes

Your repurposed laptop server should now draw 10-15W can go as low as 6 Watts at idle versus 100W+ for desktop servers. Monitor system health with:

```bash
glances  # or install htop
```

cockpit is also good for server overall monitering, system logs, managing, system status etc locally or remotely if you have routed the server into internet..

```bash
sudo apt install cockpit
sudo systemctl enable --now cockpit.socket
```

this allows you to access the server via a web browser at:
`https://localhost:9090` or `https://<your-server-ip>:9090` or `https://hostname.local:9090`

log in with your normal Linux username/password

Next Steps:

* Implement Let's Encrypt certificates
* Explore Kubernetes micro-clusters
