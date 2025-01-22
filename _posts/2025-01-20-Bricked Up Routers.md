---
title: Bricked Up Routers
date: 2025-01-20
categories: ["reverse engineering", "hardware hacking"]
tags: ["firmware", "reverse engineering", "hardware hacking"]

author: zane
description: My adventure in hardware hacking and finding vulnerabilities in SOHO devices.
---

After watching some [Matt Brown](https://www.youtube.com/@mattbrwn) videos on tearing apart hardware and reading firmware off chips, I figured "How hard could that really be?". I have a background in software reverse engineering and even pulling apart SOHO device firmware. This is my sporadic adventure into bricking devices, finding vulns, and learning a few things while I'm at it. 

First off, I needed to find some cheap devices that I wouldn't mind destroying. Good thing I have about 5 thrift stores within a 10 mile radius of my house. I had gone by a few days prior to see if I would be lucky enough to find a mint condition set of brand new skis in my size (still not lucky) and remembered seeing several devices. At my first stop I was able to grab several devices including a Centurylink Zyxel C3000Z, a TP-Link Archer C7, and a Netgear N600. All for under $20. 

![Image](https://github.com/user-attachments/assets/a183cd11-6cbf-4b26-94c5-5b3b6fdf67be)

I already owned a soldering station with a hot air gun so all I needed now was a way to read the firmware off the devices. Thanks to the power of the internet I found the xgecu t48 universal programmers for sale on Amazon and I was able to have it delivered a few days later. In the meantime, I had no idea how to remove these chips from boards other than from watching videos online. So I spent a few hours destroying chips while practicing my hot air skills. 

![Image](https://github.com/user-attachments/assets/09126997-6a97-414c-8e95-5fbb0865dead)

With my new programmer in hand and some practice desoldering chips, I was ready to read my first firmware! Mind you, these devices are all somewhat old and I could have more than likely found current firmware versions for them online. But this is much more fun. 

The first device on the chopping block will be the Netgear N600, because that's the one I am writing this post about. The other devices had some interesting things but I haven't spent enough time to identify any vulnerabilities. The firmware version that was on this device from the thrift store was V1.0.0.20_1.0.28. Great, no one knew what security was 10 years ago so this might have some good bugs!

![{7E238FE3-DD14-4FDD-96B9-C675A0D1F4B4}](https://github.com/user-attachments/assets/1b92951c-29ca-4621-a3b2-c5c518dae906)
![{86EBA325-FC5C-4DE0-AFEE-E99976B7A29C}](https://github.com/user-attachments/assets/ab679b06-68aa-476a-a01f-3f883a36fe30)


This device had a SOIC8 chip (MX25L6406E) that I in theory could read without removing it from the device but for some reason I couldn't get it to work. Fortunately, SOIC8 chips are somewhat easy to remove and I theoretically could put it back on for further testing. 

![{DE2FFF4A-EFD4-4EF7-BF34-93B7A4FA2062}](https://github.com/user-attachments/assets/65977b50-c6b4-4e64-b1fd-2aa6a85d5203)

(I was able to put it back on and the device still works!)

![{ED715188-CEE6-4595-BF2C-3ACF95E489E8}](https://github.com/user-attachments/assets/32e75b19-17a5-42dc-b0a0-42e2ed7de8c9)

With the firmware read from the chip, my first step was to run strings on it to see if anything popped out at me. 

```
4$P*
<$P*
<$P*
fJ5d
<%XA
)5$@	
	4%@	
ZSIB
FLSHh
Xboardtype=0x0550
...
0:maxp5gla0=0x3C
0:subdevid=0xbdc
apmode_dns1=
wl_antdiv=-1
wl_radio_pwrsave_level=0
wlg_defaKey=0
wla_temp_secu_type=None
wlan_acl_dev23=
0:maxp5gla1=0x3C
0:ag0=0x2
```

After that, a little binwalking and I have the squashfs directory layout to peruse at my leisure. 
```
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
14845         0x39FD          JBOOT STAG header, image id: 2, timestamp 0xA020818, image size: 68182016 bytes, image JBOOT checksum: 0x8228, header JBOOT checksum: 0x5800
38809         0x9799          JBOOT STAG header, image id: 16, timestamp 0x14021610, image size: 554713088 bytes, image JBOOT checksum: 0x88, header JBOOT checksum: 0x2100
43684         0xAAA4          gzip compressed data, maximum compression, has original file name: "piggy", from Unix, last modified: 2012-12-17 11:00:09
262144        0x40000         TRX firmware header, little endian, image size: 6647808 bytes, CRC32: 0x924001AD, flags: 0x0, version: 1, header size: 28 bytes, loader offset: 0x1C, linux kernel offset: 0x1390FC, rootfs offset: 0x0
262172        0x4001C         LZMA compressed data, properties: 0x5D, dictionary size: 65536 bytes, uncompressed size: 3614368 bytes
1544444       0x1790FC        Squashfs filesystem, little endian, non-standard signature, version 3.0, size: 5359436 bytes, 854 inodes, blocksize: 65536 bytes, created: 2013-01-09 08:23:56
7667728       0x750010        bzip2 compressed data, block size = 900k
7733264       0x760010        bzip2 compressed data, block size = 900k
7798800       0x770010        bzip2 compressed data, block size = 900k
7864336       0x780010        bzip2 compressed data, block size = 900k
7929872       0x790010        bzip2 compressed data, block size = 900k
7995408       0x7A0010        bzip2 compressed data, block size = 900k
```
I know these devices usually have their own homebrew version of an http server running so my next step was to identify that and see what I could make sense of. 

This is the squashfs usr/ structure, which houses the httpd binary. 
```
...
├── usr
│   ├── bin
...
│   ├── local
│   │   └── samba
│   │       ├── lib
│   │       │   ├── lmhosts
│   │       │   └── smb.conf -> ../../../tmp/samba/private/smb.conf
│   │       ├── lock -> ../../../var/lock
│   │       ├── nmbd
│   │       ├── private -> ../../../tmp/samba/private
│   │       ├── smbd
│   │       ├── smb_pass
│   │       └── var -> ../../../var
│   ├── sbin
│   │   ├── dhcp6s
│   │   ├── dnsmasq
│   │   ├── dnsRedirectReplyd
│   │   ├── email
│   │   ├── emf
│   │   ├── et
│   │   ├── ftpc
│   │   ├── gproxy
│   │   ├── heartbeat
│   │   ├── httpd
│   │   ├── httpsd.pem
........
│   └── tmp -> ../tmp
...
```

There's also an RSA private key sitting in the same directory but I didn't look into that too much.
```
httpd: ELF 32-bit LSB executable, MIPS, MIPS32 version 1 (SYSV), dynamically linked, interpreter /lib/ld-uClibc.so.0, stripped
```

From here I opened this binary in Ghidra and started to get a lay of the land checking strings for any weird looking content. Nothing popped out at me immediately so my next check was to see if their was any usage of functions without checking user input data (easier said than done). I found a couple uses of 'strcpy' without the n. This could be good if I could determine a user can supply the src data. 

![{8B26977E-BF2B-49BD-8327-C0DC3A882648}](https://github.com/user-attachments/assets/8f83eb6f-4f46-4882-8a0e-3a62c86dfc2c)

A side note, while checking the Authorization: Basic header, when base64 decoding the password is sent in plain text in this header. Neat. 

Now I need to figure out how to get UART working or patch the firmware to enable remote access so I can debug... it looks like telnet is enabled but for some reason it wouldn't let me connect. For now my adventure will continue in the next post where I did further into the httpd binary and possibly find some vulnerabilities. 

3 devices were bricked in the making of this post.
