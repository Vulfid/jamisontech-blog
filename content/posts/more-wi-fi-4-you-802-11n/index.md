---
title: "More Wi-Fi 4 You! | 802.11n"
date: 2024-05-21
tags: ["Wi-Fi", "802.11", "wireless"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

May 21, 2024

**802.11n – Ratified September 2009**

**“**High Throughput”

**Alternative Industry Names: **Wi-Fi 4

              You’ve heard of 20 MHz channels – now get ready for something cooler: 40 MHz!

With 802.11n, Wi-Fi would see the introduction of its first wide channel with 40 MHz. An 802.11n AP, which is broadcasting with a 40 MHz channel, can still service clients which are not capable of utilizing the bonded 40 MHz channel by simply communicating with those clients solely in the primary 20 MHz channel. This primary + secondary channel structure and naming scheme will come up often so familiarize yourself with the concept that the primary channel will be on one of those 20 MHz channels (channel 36 for example) while the secondary channel will be either +1 or -1, meaning it will be the 20 MHz channel, one below or one above, the primary. Realize that while 802.11n introduced these bonded 40 MHz channels to both 2.4 and 5 GHz, it is not advisable to utilize anything beyond 20 MHz channels in 2.4 GHz as there is simply no room in the spectrum for multiple APs to utilize 40 MHz channels which would overlap and create even more contention in 2.4 GHz.

              Unsurprisingly, wider channels mean an increase in possible data rates even with no other additions to the PHY. Luckily, we also have those! The HT PHY included the addition of a new technology to 802.11 — Multiple Spatial Streams in the form of Multiple Input/Multiple Output (MIMO). HT PHY allows for up to 4 concurrent spatial streams of data which are combined at the receiver into a greater aggregate throughput. This is where you’ll see enterprise AP models list the number of![]()“R x T : SS” they are capable of: i.e. 

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2022/06/image-11.png?w=96)

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2022/06/image-10.png?w=194)

Given the introduction of wider channels, MIMO, and the modulation schemes at play, 802.11n is capable of some impressive maximum data rates that peak at 600 Mbps with 4 Spatial Streams on a 40 MHz channel. Of course, the client STA and AP STA must both be capable of 4 SS for this rate to be achieved. At the time of 802.11n most were capped at 3 SS which meant a linear decrease to a ‘meager’ 450 Mbps. In other words, 150 Mbps per SS when a HT STA is using a 40 MHz channel. This linear relationship between maximum data rate and the number of spatial streams will continue into later amendments.

**[NOTE:** At this point, the MCS Index becomes a bit of a necessity due to how many different possible combinations of technologies result in distinct data rates.**]**    

[https://mcsindex.com/](https://mcsindex.com/)

**Bands Supported: **2.4 & 5 GHz

**Data Rates Supported: **ranged from 72.2 Mbps with a 1×1 STA on a 20 MHz channel in 2.4 GHz up to 600 Mbps with a 4×4 STA on a 40 MHz channel in 5 GHz.