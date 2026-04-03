---
title: "Covering 802.11-Prime"
date: 2022-04-22
tags: ["Wi-Fi", "802.11", "wireless"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

**802.11-1997 (Prime) – Ratified June 1997**

**“**IEEE Standard for Wireless LAN Medium Access Control (MAC) and Physical (PHY) Specifications.”

**Alternative Industry Names: “**802.11 Prime”, 802.11 Legacy Mode, 802.11 STANDARD (while others are amendments to the original standard that is 802.11 – 1997 or roll-ups of the standard like 802.11-2020)

**PHY Acronym:** Direct-Sequence Spread Spectrum (DSSS)

The original 802.11 Standard created by the IEEE. Very important note: This standard and all amendments to come include both the **Data Link (MAC) layer** and **Physical (PHY) layer** of the 802.11 protocol. The whole of the MAC + PHY state machine is outlined in the IEEE standards. This includes the sub-layers of each [Figure 11] (Carpenter et al., 2021, p. 150).

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2022/06/image-5.png?w=514)

Note that this is not the original ISO/IEC model, as this includes the RSNA Key management which we will see is not implemented until 802.11i in response to vulnerabilities observed in WEP & TKIP/WPA (Wi-Fi Alliance). It also features mention of the 802.1X roles served by client and AP STAs in an 802.1X association, this however is an entirely different IEEE standard which 802.11 utilizes and not a part of the 802.11 standard itself. What this model does show is the logical state machine outlined in the IEEE’s 802.11 standard. As you investigate the different functions of the 802.11 protocol in both the MAC and PHY, pay close attention to how data is handed down/up the stack and what is done at each sublayer (PLCP vs PMD for example, and their functions).

802.11 – 1997 allowed for three physical layer technologies: 

- Infrared at 1 Mbps data rates - Frequency-Hopping Spread-Spectrum (FHSS) at 1 Mbps and 2 Mbps data rates - Direct-Sequence Spread-Spectrum (DSSS) at 1 Mbps and 2 Mbps data rates 

FHSS and DSSS use the unlicensed 2.4 GHz Industrial Scientific Medical (ISM) frequency band. DSSS allows for up to fourteen 22 MHz wide channels (three non-overlapping), while FHSS utilizes 79 non-overlapping 1 MHz wide channels. 

802.11 was originally written for compliance in the United States, Japan, Canada, and Europe only. DSSS in 2.4GHz was the most widely used technology from this standard and for that reason it would serve you to recognize a few key details that make DSSS and its channel structure different. If you are familiar with modern 802.11ac/ax wireless networks in the enterprise, the 22 MHz wide channels used with DSSS in 802.11 – 1997 should stand out to you. The wider channel width meant that the side-band interference for which you and I know 2.4 GHz so well was even worse before the introduction of the OFDM channel sizes and technology with 802.11a (more on that to come).

**[NOTE:** For reference, a 22MHz wide channel, on Channel 1 with its center at 2.412 GHz for example, would use 11 MHz on either side of that center frequency for the transmission. Meaning Channel 1 ranged from 2.401 GHz up to 2.423 GHz.]

DSSS allowed for only a single spatial stream — SISO (Single-input/single-output). The transceiver (transmitter/receiver) sends one stream of data or receives one stream of data at a time. As we continue you will see the addition of (up to 8) spatial streams in Wi-Fi communications allows for significant improvements in available data rates. In fact, the relationship between the number of SS possible and data rate is a linear increase for each additional SS. All future 802.11 STAs still support DSSS PHY unless otherwise disabled by some compounding setting (like G-only or N-only modes on future AP STAs).

**Bands Supported: **2.4 GHz (The ISM Band) [Channel 1 at 2.412 GHz – Channel 14 at 2.484 GHz] – ~100 MHz of Spectrum.

**Data Rates Supported: **1, 2 Mbps (in 2.4 GHz)

Works Cited

Carpenter, T., Foster, L. D., Dionicio, R., & Harkins, B. (2021). *CWAP-404: Certified Wireless Analysis Professional – CWNP, LLC* (2nd ed.). CertiTrek Publishing.