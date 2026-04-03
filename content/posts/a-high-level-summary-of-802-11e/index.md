---
title: "A High-Level Summary of 802.11e"
date: 2024-02-16
tags: ["Wi-Fi", "802.11", "wireless"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

**802.11e – Ratified September 2005**

“MAC Enhancements QoS”

802.11e would extend QoS service into the wireless medium through added functionality at the MAC layer. This was accomplished through prioritization of frames based upon traffic class. Originally, 802.11 relied upon Distributed Coordination Function (DCF) which itself relied upon Carrier-Sense Multiple Access/Collision Avoidance (CSMA/CA) to minimize collisions occurring over-the-air. CSMA in turn would utilize two significant steps in its avoidance of collisions: Carrier-Sense (CS) and Energy-Detect (ED). “Carrier-Sense is the process of checking to see if the medium is in use or busy” (Carpenter et al., 2021, p. 327). Now, we’re continuing down the ‘nested’ rabbit hole because Carrier-Sense itself was performed in two different varieties for 802.11 WLANs: Virtual Carrier-Sense and Physical Carrier-Sense. Physical CS utilizes Clear Channel Assessment (CCA) to discover whether the physical medium is in use and is thusly provided by the PHY to the MAC layer of the 802.11 protocol, whereas Virtual CS utilizes a Network Allocation Vector (NAV), provided by the MAC, which sets a timer for how long an STA must wait before attempting to transmit after observing a competing STA’s frame duration field value.

Between detecting the presence of energy on the RF medium and responding appropriately to reported transmission intervals observed in 802.11 frames, I believe that the coordination functions are some of the most interesting aspects of the protocol and would advise that you seek out more detailed writings on how that technology conducts itself. It’s a wonder any of this stuff works!

Getting back on topic, 802.11e would better the existing DCF (and the seldom-implemented PCF) by means of the Hybrid Coordination Function (HCF). HCF itself contained two methods of channel access: HCF Controlled Channel Access (HCCA) and Enhanced Distributed Channel Access (EDCA). Present in both is the concept of traffic categories. The 4 defined traffic/Access Categories (AC) as follows:

- Background (AC_BK)

- Best Effort (AC_BE)

- Video (AC_VI)

- Voice (AC_VO)

Each of the Access Categories defined in EDCA maps to a particular CWmin, a minimum contention window which determines a minimum value that each traffic category must wait before attempting to access the medium. When a transmission is sent and fails to be acknowledged, that frame’s CWmin is doubled, +1. This increase will occur each time the frame must be re-transmitted until the maximum window is reached and eventually the frame abandoned after 32 re-transmission attempts. Below is a mapping of Access Categories to CWminimums/maximums for 802.11a/n. (“IEEE 802.11e-2005, Wikipedia”, 2022)

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2022/06/image-3.png?w=611)

One thing noted in this table but not yet written about here is Arbitrary Inter Frame Spacing Number (AIFSN) which is calculated as

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2022/06/image-4.png?w=470)

For each PHY there is a standardized SIFS time value *AND* a separate per-PHY slot-time value. The space between every transmitted frame in an 802.11 exchange is dependent upon which Interframe-Spacing type is defined to follow that frame sub-class.

We’ve now defined AIFSN and mentioned that SIFS is defined per-PHY and used in the calculation of other Interframe-Spacing (IFS) values. SIFS stands for short-interframe-spacing and, like slot-time, is utilized in the calculation of the other interframe-spacing timings. The effect of this is that a particular PHY with a shorter-defined SIFS value/slot-time might have access to the medium slightly before another PHY.

**NOTE:** I would refer you to an external resource to study these interframe spacing types in more detail (Pindoria, 2017). Additionally, the CWAP text itself does a great job explaining interframe spacing. If you’re craving a mnemonic to remember the interframe-spacing names and their order from shortest to longest: RSP DAE “Raspy Day” – when said aloud.

## RSP DAE

Pindoria, A. (2017, May 18). *Interframe spaces (Rifs, sifs, pifs, DIFS, AIFS, EIFS)*. Dot11AP. [https://dot11ap.wordpress.com/interframe-spaces-rifs-sifs-pifs-difs-aifs-eifs/](https://dot11ap.wordpress.com/interframe-spaces-rifs-sifs-pifs-difs-aifs-eifs/)

February 16, 2024