---
layout: article
title: Bitscout basic usage
key: basic-usage
cover: /assets/images/skyline.png
article_header:
  height: 1vh
  type: overlay
  theme: dark
  background_color: '#123'
  background_image: false
---

This page shows basic usage of the tool
<!--more-->

# Prerequisites #  
If you are new to Bitscout concept, make sure you go through the Glossary to understand some of the terms used below.  

# Preparing Bitscout ISO File #  
To generate new ISO file you will need Ubuntu system (tested on Ubuntu 18.04). If you are running on MacOS X or Windows, you can boot from Ubuntu Live CD instead (download at https://www.ubuntu.com/download/desktop) or install it on a virtual machine.  
Once you have Ubuntu running, start a terminal and follow the instructions below:  
1. Install git  
`$ sudo apt-get install git`
1. Download project from Github  
`$ git clone --recursive https://github.com/vitaly-kamluk/bitscout`  
1. Enter project directory and start build process  
`$ cd bitscout`  
`$ ./automake.sh`  
The process will ask you basic questions regarding designated size of image, custom kernel and VPN settings. When the process is finished you shall find a freshly generated ISO file in current directory.  
  
If you are unsure if you are doing it right, watch this video of early version build process:  
https://www.youtube.com/watch?v=knA0NS9tWsY

Note:  
The ISO creation may take from minutes to several hours depending on your internet bandwidth, preferences set in the beginning of the process and computing power of your PC.  
If you want just to play with Bitscout or use for quick research, we suggest to build medium size ISO without custom kernel compilation. This shall save you time for building while including some essential tools in the system. However, if you need to use Bitscout for real world forensics, we strongly recommend to use custom kernel at least (may increase ISO creation time for 3 hours on a single core CPU). Custom kernel includes kernel write-blocker patches to avoid accidental or unintended disk modification (for more information see https://github.com/msuhanov/Linux-write-blocker).  

Note 2:
Bitscout was started on Ubuntu Xenial (16.04) and we still keep that branch in the git repository. Feel free to switch the branch to 16.04 and build an older version. To switch to 16.04 branch, simply run:  
`git checkout -b 16.04 origin/16.04`  
`git pull`  

# Creating Bootable Media #
Bitscout ISO file is a hybrid image file, which can be used to create bootable CD or USB drive. Once you have the ISO file you can burn this image on a CD using the same Ubuntu system used for building. You can use simple CD-burning software called Brasero which comes with Ubuntu.  
If you need to create bootable USB all you need is to run a simple command in the terminal after your ISO file is built:  
`$ sudo dd if=./bitscout-18.04.iso of=/dev/sdX`  
Where /dev/sdX is your designated USB storage device. Make no mistake when choosing the device name or you can lose data on your harddrive. Bitscout authors take no responsibility for the mistakes you make. To inspect attached disk drives and respective partitions use the following system command: `$ sudo lsblk`.  
  
# Setting Up Your Bitscout Server #
To use Bitscout remotely you need to have own server to run VPN service. There are no formal requirement for the base OS of the server as long as it can run OpenVPN. You can use free opensource Linux system for this (i.e. Ubuntu server, see https://www.ubuntu.com/download/server).  
  
The following instructions assume that you are running Debian-based Linux server such as Ununtu server.  
  
## Setup OpenVPN ##  
1. Install openvpn package  
`$ sudo apt-get install openvpn`  
1. Copy your OpenVPN config and keys from Bitscout build machine (bitscout/exports/etc/openvpn/) to /etc/openvpn/.  
1. Start OpenVPN instance  
`$ sudo systemctl start openvpn@scout.service`  
  
For more information or troubleshooting, please see official OpenVPN HowTo at  
https://openvpn.net/index.php/open-source/documentation/howto.html. 
  
## Additional OpenVPN Users ##  
To add more users who can connect to your VPN server, you need to generate certificates for them or use shared certificates.  

### Additional Certificates Generation ###  
Below is a simple instruction on how to generate a new certificate for the new user, i.e. user named "alice":  
1. Navigate to your current Bitscout build directory in the terminal.  
1. Go to EasyRSA directory (if your ISO build was successful):  
`$ cd ./config/openvpn/easy-rsa`  
1. Apply EasyRSA settings to current shell environment:  
`$ . ./vars`  
1. Create certificate for user "alice":  
`$ ./build-key alice`  
1. Navigate to the Bitscout build directory clone the expert's files to alice's own directory:  
`$ cd ../../../ && cp -r ./exports/expert ./exports/alice`  
1. Replace the expert's keys inside alice's export directory with the new keys of alice:  
`$ cp ./config/openvpn/easy-rsa/keys/alice.key ./exports/alice/etc/openvpn/scout/expert.key`  
`$ cp ./config/openvpn/easy-rsa/keys/alice.crt ./exports/alice/etc/openvpn/scout/expert.crt`  
1. Share ./exports/alice directory with user alice.  

### Using Shared Certificates ###  
This solution is not recommended because it may be hard to track which user was connected at specific time. Still in some in-lab experimental environment this can be quick and useful. If you use this scenario, you can share the expert's VPN keys/config with any number of users. All you need to do is to add the following option to your OpenVPN server config on a new line and restart the VPN service:  
`duplicate-cn`  


## Setup Chat Server ##  
As far as Bitscout suggests remote cooperation of 2+ users it makes sense to use simple chat to coordinate actions. We suggest to use lightweight IRC communication for that.  
1. Install IRC service  
`$ sudo apt-get install ngircd`  
1. Copy ngircd.conf file from Bitscout build machine (bitscout/exports/etc/irc/ngircd.conf)  
1. Start IRC service  
`$ sudo systemctl start ngircd.service`  

# Bitscout Components #  
## Startup Process ##  
To start Bitscout you must change your system boot order to enable booting from removable media (CD-Rom or USB). If the media with Bitscout is supported you shall see Bitscout boot menu. It will automatically boot in normal mode in 10 seconds if no keys are pressed.  

## Bitscout User Credentials ##  
Bitscout doesn't set any passwords for local user called *user* both in container and the host. The isolation of environment and remote access based on SSH keys and VPN certificates removes the need to use passwords. So the *user* password for sudo operations is empty.
  
## Bitscout Management Tool ##  
When Bitscout is started successfully it runs the management tool. There is no GUI on purpose for the sake of speed, low memory usage and compact size.  
Bitscout Management Tool is simple tool (well, definitely simpler than a command line) which shall help a regular PC user to do basic system configuration and assist the expert. The tool consists of menus and submenus which can be navigated using keyboard arrows and Enter keys.  

### Network Configuration ###  
In most of the cases Bitscout doesn't require any configuration, because everything is automated to provide remote expert with access to the guest container. However, if the system owner uses static IP instead of DHCP, it has to be set manually in **Network -> Static IP -> eth0**.  
If internet access requires proxy with authentication it can be set in **Network -> HTTP Proxy** or **Network -> Socks Proxy**.  
  
Bitscout can be used on systems where the only way to access the internet is to use WiFi. There is **Network -> WIFI Settings** menu for that, which can start Network Manager known as wicd-curses. To read more about how to use it, please refer to it's own documentation (see http://manpages.ubuntu.com/manpages/precise/man8/wicd-curses.8.html).  

### Disk Operations ###  
By default Bitscout shall treat all disk drives as read-only media and no disks are made available within the expert's container. However, to get the remote analysis started disks have to be mapped from the host to the container. This is done via **Disk -> Map** menu. To view current disk drives and the list of existing mappings use menu **Disk -> View**. After the disk is analyzed it can be unmapped via **Disk -> Unmap**.  
While some filesystems, such as Windows NTFS can be mounted with userspace filesystem driver (FUSE), others may require kernel-mode driver and privileged mount operation which is available only to power-users. As far as expert's container is running under unprivileged user (with simulated root in it) it's not possible to mount such filesystems. To resolve this problem a temporary escalation of privilege is required. The expert can use alternative command **mount.priv** (instead of mount) to request privileged mount operation. While such request are carefully controlled, logged and parameters are sanitized, it may be a critical to overall system security. This is why such operations shall be supervised and this feature is disabled by default. It is recommended to keep it disabled at all time when it's not supervised. To enable/disable this feature use **Disk -> Privileged Mode**.  
  
### Status Monitoring ###  
Bitscout let's the system owner monitor basic system status and remote terminal sessions via **Status** menu. Once a new user connects via SSH a new window will be created to track that session live. The status monitoring script runs inside tmux session. To close this session and return to the main menu, the owner can close all opened windows by pressing Ctrl+C or use tmux hotkey to detach from current session (Ctrl+b d). For more information on tmux commands, please refer to the official manual (see https://github.com/tmux/tmux/wiki).

### Chat ###  
Bitscout comes with an IRC client which is a console chat application called **irssi**. It is preconfigured to connect to the expert's private IRC server via VPN link. The owner can enter chat room by navigating to **Chat** menu in the management tool. To quit from IRC client and get back to the management tool type **/quit** and press Enter in irssi.  

### Other Features ###  
Bitscout provides no restriction to the system owner to use any features of Linux OS or any software (open source or own proprietary software). If the owner is experienced in Linux commandline Bitscout can be managed via shell. There are two menu items for this. **Container Shell** is used to start a shell as root inside the guest container, while **Shell** provides the owner a way to work as root on the host system.  
Both guest container and the host systems can be extended by installing additional software from repositories. Please note that all index files were removed to reduce the ISO file size, so make sure you run `$ sudo apt update` before installing additional packages (`$ sudo apt install pkgname`).  
## The Expert's Environment ##  
The expert works within unlrivileged Linux container environment on Bitscout. This creates constraints and introduces even some complications comparing to a normal physical host. While expert can run as root within the container this is a virtual root. Below is a short list of things an expert can and cannot do, which is enforced by software constraints.  
### The Expert Can Do ###  
1. Change software configuration and install additional software.  
1. List attached physical drives and view basic properties (such as size, partition names).  
1. Mount/unmount filesystems (using fuse fs driver or using privileged commands such as **mount.priv** and **umount.priv**).  
1. List kernel modules
1. Download/upload files from/to the system.

### The Expert Can Not Do ###  
1. Load/unload kernel modules.  
1. Mount/unmount filesystems using kernel modules without owner's permission.  
1. Change host system configuration or install any software on it.
1. Download/upload/access any files on the host.  
1. Poweroff or reset the host system.  

### Accessing Disk Devices ###
If the owner authorised certain devices for analysis or read-write usage, they will be attached to loop devices inside the container and appear as one of the following paths.
Read-only mappings:  
`/dev/host/evidence0`  
`/dev/host/evidence1`  
`/dev/host/evidence2`  
`...`  

Read-write mappings:  
`/dev/host/storage0`  
`/dev/host/storage1`  
`/dev/host/storage2`  
`...`  
  
The exact name of the device depends on the owner's choice in the management tool (**Disk -> Map**).  

### Privileged Mounting ###
To use privileged mount command simply try running **mount.priv** instead of regular mount command. If you see a message saying that privileged operations were not enabled, you have to ask the owner to temporarily enable privileged mode via Bitscout management tool and **Disk -> Privileged Mode -> Enable** menu. Please note, that mount.priv supports only limited authorized set of local filesystems. Using automatic filesystem detection is not allowed amd you must specify type of the filesystem to mount. Similarly, you must use read-only option to mount evidence disks accessible in read-only mode of operation will fail with error. Below is an example of mounting ext4 filesystem in readonly mode:  
`# mount.priv -t ext4 -o ro /dev/host/evidence0 /mnt/evidence0`  
Similarly there is **umount.priv** that is used to unmount filesystems.

# Using Bitscout #  
## Disk Acquisition ##  
Below is the description of a remote disk image acquisition process using Bitscout, where the system owner is non-experienced commandline user (relies on Bitscout management tool only). 
### The owner ###  
1. Maps the evidence drive from host to the experts container using Bitscout management tool (see Disk Operations above).
Let's assume that it was mapped as evidence0.  
1. Attaches output storage device (can be removable USB hdd) to the host system.  
1. Maps the attached output storage partition to writable device on the container. Lets assume it is mapped as storage0.  
### The expert ###  
1. Mounts the attached output drive filesystem on local directory (i.e. /mnt/storage0).
1. Acquires disk image using dd or it's clone. For example:  
`# dc3dd if=/dev/host/evidence0 of=/mnt/storage0/image.dd bs=4k hash=md5 log=/mnt/storage0/image.dd.log progress=on`
1. Unmounts the output filesystem.  
### The owner ###  
1. Unmaps evidence0 and storage0.
1. Disconnects the output storage device.