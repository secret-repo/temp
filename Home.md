---
layout: article
title: Bitscout Project
key: bitscout-project
cover: /assets/images/skyline.png
article_header:
  height: 1vh
  type: overlay
  theme: dark
  background_color: '#123'
  background_image: false
---

# Bitscout #  
Bitscout is an open-source and free tool developed by security researchers for all people who do or eager to learn digital forensics and cyber investigations.  
<!--more-->

# Bitscout Promise #  
Bitscout is and will remain transparent and open-source project with open and reliable architecture that protects the system owner's interests in cyber investigations. We adopted the Hypocratic Oath, which essentially is "First do no harm".  

# Getting Started #  
Bitscout ISO can be built on any Debian-based Linux system, including running from a LiveCD. 

We tested building process on Ubuntu 18.04. Whether you are Mac OS X or Windows user you don't need to install Linux to build Bitscout: fetch and run the builder using Ubuntu LiveCD, save produced ISO file to USB drive and use it. However, for the long term it's recommended to setup a dedicated Bitscout building VM. This way you can quickly change some settings, add/remove packages without need to run everything from the beginning.

To see how it is done, please check out this video(please note that Bitscout is constantly improved and changed, so your build process may be different): <br/>
https://www.youtube.com/watch?v=knA0NS9tWsY

Simple steps are required from the user who started Ubuntu LiveCD and wants to build Bitscout:
1. Install git:<br/>
`$ sudo apt install git`
2. Fetch the project files from Github:<br/>
`$ git clone --recursive https://github.com/vitaly-kamluk/bitscout`
3. Start the builder<br/>
`$ cd bitscout`<br/>
`$ ./automake.sh`
4. Choose build size and answer the basic questions about VPN. Wait for the build process to complete.
5. Find your freshly built ISO file in the same directory:<br/>
`$ ls -la`  

For more information please refer to other articles (i.e. **Basic Usage**).


