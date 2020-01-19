---
layout: article
title: Glossary used in this project
key: glossary
cover: /assets/images/skyline.png
article_header:
  height: 1vh
  type: overlay
  theme: dark
  background_color: '#123'
  background_image: false
---

The following terms are used.
<!--more-->

1. **Bitscout**  
The project name, but also a reference to a running instance of OS running from removable media build with the help of scripts included in the project.  
1. **The owner**  
The user and owner of the system which is running Bitscout.
1. **The expert**  
A user remotely connected to a running Bitscout via VPN+SSH.
1. **Host system** (also Host)  
A computer system which was started from Bitscout removable media.  
1. **Guest system** (also Guest, Guest container, Expert's environment)  
A virtual system running on host system using unprivileged LXC container.  
1. **Disk Mapping**  
An operation to attach disk(block) device from the host system to the guest container. After this operation the disk can be accessed by the expert.  
1. **Privileged Operation**  
This is relevant to the guest container only. A system command or operation which is initiated on the guest container but is executed with elevated privileges on the host.  
1. **Filesystem Mounting**  
Attaching filesystem contents with underlying directories and files to a local directory.  
1. **Filesystem Privileged Mounting**  
Same as Filesystem Mounting but using privileged mount command (mount.priv).  
