---
title: "802.11a | Wi-Fi! Now in 5GHz!"
date: 2022-04-27
tags: ["Wi-Fi", "802.11", "wireless"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

April 27, 2022

802.11a – Ratified September 1999

**“**Higher Speed PHY Extension in the 5 GHz Band”

**Alternative Industry Names: **Wi-Fi 2

**PHY Acronym:** Orthogonal Frequency Division Multiplexing (OFDM) PHY

Ratified in tandem with 802.11b, 802.11a allowed for data rates up to 54 Mbps in the 5 GHz U-NII band by utilizing Orthogonal Frequency Division Multiplexing (OFDM). 802.11a specified twelve non-overlapping 20 MHz wide channels in the Unlicensed National Information Infrastructure (U-NII) frequency bands.

**[Note:** This was the introduction of not just a new bandwidth to 802.11, but a new modulation scheme. OFDM would at this point only be available in the 5 GHz band, meaning that HR-DSSS/DSSS would still be present in 2.4 GHz and utilizing those 22 MHz wide DSSS channels, despite the introduction of OFDM’s 20 MHz wide channel structure in 802.11a– which persists to this day in 802.11ax. (With new additions of course)**].**

OFDM still utilizes a single spatial stream. However, due to the enhanced modulation in 802.11a, it is capable of supporting data rates of 6, 9, 12, 18, 24, 36, 48, and 54 Mbps. You’ll note that OFDM/802.11a does NOT support the previous data rates which 802.11b introduced into 2.4 GHz because this is a new band. From here on out (in 5 GHz at least) 6 Mbps will remain the lowest data rate you’ll see.

[A question for you: *At what data rate would an **802.11n** management frame be transmitted?*] Answer at page end.

**Bands Supported: **5 GHz

**Data Rates Supported: **6, 9, 12, 18, 24, 36, 48, and 54 Mbps.

[**Answer**: 6 Mbps unless the vendor has implemented otherwise. Management frames will typically be transmitted at the minimum basic data rate of the bandwidth.]  

For reference, this 802.11 spectral mask diagram shows the general shape of the signals. (Ng, S.W.K. & Szymanski, Ted., 2012). You can see how the width and shape of the spectrum changes with OFDM

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2022/06/image-7.png?w=820)

Works Cited

Ng, S.W.K. & Szymanski, Ted. (2012). Interference measurements in an 802.11n Wireless Mesh Network testbed. Canadian Conference on Electrical and Computer Engineering. 1-6. 10.1109/CCECE.2012.6334846.