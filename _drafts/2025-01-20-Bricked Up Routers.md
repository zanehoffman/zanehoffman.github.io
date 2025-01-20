---
title: Bricked Up Routers
date: 2025-01-20
categories: ["reverse engineering", "hardware hacking"]
tags: ["firmware", "reverse engineering", "hardware hacking"]

author: 1
description: My adventure in hardware hacking and finding vulnerabilities in SOHO devices.
---

## Background
After watching some [Matt Brown](https://www.youtube.com/@mattbrwn) videos on tearing apart hardware and reading firmware off chips, I figured "How hard could that really be?". I have a background in software reverse engineering and even pulling apart SOHO device firmware. This is my sporadic adventure into bricking devices, finding vulns, and learning a few things while I'm at it. 

First off, I needed to find some cheap devices that I wouldn't mind destroying. Good thing I have about 5 thrift stores within a 10 mile radius of my house. I had gone by a few days prior to see if I would be lucky enough to find a mint condition set of brand new skis in my size (still not lucky). Fortunately, at my first stop I was able to grab several devices including a Centurylink Zyxel C3000Z, a TP-Link Archer C7, and a Netgear N600. All for under $20. 

![my first victims](https://cdn.discordapp.com/attachments/1043371683421110423/1331026523154808894/IMG_3516.jpg?ex=67901e8c&is=678ecd0c&hm=9c2ea93c0fb2d9a3bb991341a12cf6a57bd44514e0776b956360f1937fbb4552&){: width="700" height="400" }

I already owned a soldering station with a hot air gun so all I needed now was a way to read the firmware off the devices. Thanks to Matt Brown's YouTube description, I found xgecu t48 universal programmers for sale on Amazon and with prime I was able to have it delivered a few days later. In the meantime, I had no idea how to remove these chips from boards other than from watching videos online. So I spent a few hours destroying chips while practicing my hot air skills. 

![broken stuff](https://cdn.discordapp.com/attachments/1043371683421110423/1331026528213143604/IMG_3509.jpg?ex=67901e8d&is=678ecd0d&hm=2b8fb6ef06b3a5786b55e47c7bc19b2674212773ae97626bca522611fb147a56&){: w="700" h="400"}

With my new programmer in hand and some practice desoldering chips, I was ready to read my first firmware! Mind you, these devices are all somewhat old and I could have more than likely found current firmware versions for them online. But this is much more fun. 

The first device on the chopping block will be the Netgear N600, because that's the one I am writing this post about. The other devices had some interesting things but I haven't spent enough time to identify any vulnerabilities. 

