---
layout: article
title: Customisations
key: cutomisations
cover: /assets/images/skyline.png
article_header:
  height: 1vh
  type: overlay
  theme: dark
  background_color: '#123'
  background_image: false
---

This section describes instructions to modify Bitscout configuration and script files to accommodate your requirements. Note: this may NOT be applicable to older Bitscout branch (16.04).  
<!--more-->

# Customization Approach #  
Please analyse current scripts before customizing them. Here are some general advice for Bitscout contributors:
1. Stick to current design and template-based strategy.  
1. Make the code as short and as readable as possible. Do not overload it with complex expressions.  
1. If you add another stage (script) into **automake.sh** make sure that running it repeatedly does not break the final product. I.e. if your script adds another user, make sure that it verifies first that this user has not been added already.  

You should be able to add modification to files inside ./resources and ./scripts directory and rerun only those scripts that apply your changes to the final product. There is no need to restart build process from scratch every time.

Should you need to add some manual temporary modifications (i.e. install/remove/configure software packages) you can enter target filesystem using **./scripts/chroot_enter.sh** script. To quit, press Ctrl+D or type *exit* and press Enter.

# Bitscout Release Size Levels #  
Bitscout offers 3 basic release size level: minimal (1), optimal (2) and maximum (3). Depending on this, it includes libraries and software that you may want to have during forensic operation. You need to find your favorite size depending on media size, acceptable ISO file network transfer time, etc. You can change your release size level to another one at any time in **GLOBAL_RELEASESIZE** variable in `config/bitscout-build.conf`.  

# Bitscout Cryptographic Keys Length #  
By default, Bitscout uses public key cryptography (SSH, OpenVPN) with 2048-bit keys. Should you need to increase the keylength change **CRYPTOKEYSIZE** variable in `config/bitscout-build.conf`.  

# Custom Kernel # 
Config file `config/bitscout-build.conf` also offers to change the choice of using custom kernel (variable **GLOBAL_CUSTOMKERNEL**). In this case Bitscout will compile a custom kernel for you and apply kernel patch to enforce write-blocking mechanism on the kernel level. Credits for the patch go to Maxim Suhanov (more details in his project: https://github.com/msuhanov/Linux-write-blocker).  

# VPN Settings #  
During initial run, it will prompt for VPN Server, protocol and port number which shall not be prompted on subsequent run, however it can be modified manually.  
To modify the VPN settings:  
1. Go to config folder  
`cd ./config`  
1. Open bitscout-build.conf for editing, using a text editor (e.g. vi)  
`vi bitscout-build.conf`  
3. Update the entries accordingly  
`GLOBAL_VPNSERVER=new_vpn_server`  
`GLOBAL_VPNPROTOCOL=new_protocol_type`  
`GLOBAL_VPNPORT=new_port_number`  
4. Save the config file.  
5. When `automake.sh` script is executed again, it will use the settings from `config/bitscout-build.conf`
Note that you do not need to re-download all files when rebuilding after change of settings.  

# Architecture #  
Bitscout constructor builds 64-bit system by default. However, if you need to run on older 32-bit setup, you may want to use 32-bit build. In this case you can change the base architecture in `config/bitscout-build.conf` file (change **GLOBAL_BASEARCH** variable from **amd64** to **i386**) before making the ISO file.

