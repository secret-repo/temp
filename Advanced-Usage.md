---
layout: article
title: Additional Recipes To Use Bitscout
key: additional-recipes-to-use-bitscout
cover: /assets/images/skyline.png
article_header:
  height: 1vh
  type: overlay
  theme: dark
  background_color: '#123'
  background_image: false
---

This page describe some advanced scenario
<!--more-->

# Additional Recipes To Use Bitscout #  

## Pre-requisites ##  
The following recipes are valid for the users who 
1. successully established VPN connection to Bitscout container and got shell on it;  **10.1.0.2** is assumed to be the Bitscout instance over VPN. It may differ in your case if you use custom settings. The following description uses this IP as a reference to the Bitscout host over VPN.
1. mapped the source drive with the help of system owner to device named evidence0 (**/dev/host/evidence0** on the container).  The device name may also differ in your case, but the following instructions assume this is the whole disk mapping on the expert's container.

## Disk Acquisition Over The Network ##  
Sometimes expert may need to transfer the remote image over the network (if bandwidth allows and the image is relatively small). This can be achieved by using any of the methods below.

### Simple Network Client Transfer ###  
As far as there is TCP/IP connection over OpenVPN link between the expert's host (running where the expert physically is) and the expert's container (running on Bitscout), it's possible to use simple standard Linux tools to transfer any file, including whole block device as following.  
1. Start listening TCP server on the container and redirect input from the source block device:  
`# nc -l -p 2000 < /dev/host/evidence0`  
Note that ports 2000-2009 are forwarded via VPN directly to the container.
1. Start network client on the expert's host and connect to the listening server over VPN:  
`$ nc 10.1.0.2 2000 > evidence0.dd`  

To optimise the process you may add compression/decompression:  
1. On the container:  
`# cat /dev/host/evidence0 | gzip -c | nc -l -p 2000`  
1. On the expert's host:  
`$ nc 10.1.0.2 2000 | gzip -dc > evidence0.dd`  

If the disk is large or the network is slow you can use "pv" tool to monitor the transfer progress and speed. In this case you may want to run the following commands:
1. On the container:  
`# cat /dev/host/evidence0 | pv -s $(blockdev --getsize64 /dev/host/evidence0) | gzip -c | nc -l -p 2000`
1. On the expert's host:  
`$ nc 10.1.0.2 2000 | gzip -dc | > evidence0.dd`  

### Using Network Block Device ###  
If the network is unstable you may want to make use of the whole block device. It's also very useful in other scenarios, such as booting a virtual machine from the remote disk drive on the expert's host. In this case you need to use NBD (network block device) service. Starting from the medium size build, Bitscout comes with pre-installed nbd-server. Here is how you can make block device on the container appear locally on the expert's host:
1. On the container, mark the designated block device for export via nbd-server.  
Edit **/etc/nbd-server/config** to open start nbd-server listening on VPN-accessible port and add device information to export. Here is sample configuration file that does it:  
`[generic]`  
`# If you want to run everything as root rather than the nbd user, you`  
`# may either say "root" in the two following lines, or remove them`  
`# altogether. Do not remove the [generic] section, however.`  
`        user = nbd`  
`        group = nbd`  
`        includedir = /etc/nbd-server/conf.d`  
`        port = 2000`  
`# What follows are export definitions. You may create as much of them as`  
`# you want, but the section header has to be unique.`  
`[export]`  
`exportname = /dev/host/evidence0`  

1. restart nbd-server on the container:  
`# systemctl restart nbd-server.service`  

1. On the expert's host, load nbd kernel module and you can map the remote nbd export to your local nbd device:  
`# modprobe nbd`  
`# nbd-client -N export 10.1.0.2 2000 /dev/nbd0`

Note, that export is the own name of exported device. It may be changed, as well as port number or nbd device path. If the operation is successfull you should see something like  
`Negotiation: ..size = ****MB`  
`bs=1024, sz=******** bytes`  

After that the **/dev/nbd0** will contain data mapped from the remote export on the container, which enables the expert to acquire full disk image using regular dd, dc3dd or similar tools, i.e.  
`# dc3dd if=/dev/nbd0 hash=md5 of=./evidence0.dd log=./evidence0.dd.log`  
The evidence0.dd.log file will contain MD5 of the transfered disk image once the operation is completed.
