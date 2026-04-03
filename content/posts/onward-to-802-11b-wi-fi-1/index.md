---
title: "Onward to 802.11b | Wi-Fi 1"
date: 2022-04-24
tags: ["Wi-Fi", "802.11", "wireless"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

April 24, 2022

802.11b – Ratified September 1999

**“**Higher Speed PHY Extension in the 2.4 GHz Band”

**Alternative Industry Names: **Wi-Fi 1

**PHY Acronym:** HR/DSSS, High Rate/Direction Sequence Spread Spectrum PHY

802.11b would introduce data rates of 5.5 Mbps and 11 Mbps to Wi-Fi. This was accomplished with two different alternatives to the modulating and coding schemes: Complementary Code Keying (CCK) or Packet Binary Convolutional Coding (PBCC). 802.11b is commonly referred to as High-Rate Direct-Sequence Spread-Spectrum (HR-DSSS).

**[NOTE: **As we cover all the different technologies that enabled higher speeds in 802.11 connections, I would advise you avoid a misconception I initially held for years: The data rate of the MAC frame is sent at the rate indicated, but the PHY information is sent at the lowest supported data rate in the BSS. I.E.: On a BSS where the agreed upon data rates between client STA and AP STA are 6 Mbps, 12 Mbps, etc., The MAC Frame itself will be transmitted (that is to say, the combined modulation and coding scheme used to transmit and receive the frame) at the highest data rate which the STAs can achieve. However, the PHY Preamble of all frames will be transmitted at the LOWEST data rate supported in the BSS. Even if you administratively disabled 6 Mbps as a basic data rate in an 802.11a network, the PHY would still be transmitted at 6 Mbps. Let me prepare you for one other knowledge-bomb: Management frames are *not *transmitted at the same data rate as data frames.**]**

Most of the increase in data rate is due to the introduction of a more capable encoding scheme, that of CCK. To contextualize the combinations of coding rates, guard intervals, spatial streams, and modulations I would recommend at least familiarizing yourself with the MCS index here: [https://mcsindex.com/](https://mcsindex.com/) you will need to refer to it fairly often to determine data rates when numerous combinations of technologies are at play.

**Bands Supported: **2.4 GHz

**Data Rates Supported: **5.5 Mbps, 11 Mbps (in 2.4 GHz)