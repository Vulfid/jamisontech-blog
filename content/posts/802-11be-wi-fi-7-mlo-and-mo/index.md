---
title: "802.11be, Wi-Fi 7 | MLO and Mo’!"
date: 2024-06-23
tags: ["Wi-Fi", "802.11", "wireless", "Wi-Fi 7"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

If you’re in the wireless space as a profession then you have, at least, heard whispers of the beginnings of Wi-Fi 7 devices arriving on the scene. The amendment to 802.11, labelled as Wi-Fi 7 by the industry and as 802.11be by the IEEE is poised to bring another set of speed, modulation, and frequency-use improvements to our favorite medium in the coming years that is more widely-adopted. Though Wi-Fi 6E feels just out the door, I have in my current role been quickly involved in the testing and verification of some of the first bits of hardware on the AP and client front which will enabled these features. 
    I wanted to write a bit of a post on one of the more interesting features (IMO of course, I know 320 MHZ-Wide channels are cool too!) that particular feature, of course, being the addition of Multi-Link Operation (MLO). MLO intends to increase a client devices use of multiple wireless interfaces (read, radios) by utilizing multiple links simultaneously to send and receives traffic across multiple bands (2.4, 5, 6GHz). 
     You can imagine that the use of multiple bands and radios on the client device would reduce latency, allow for redundant links in the wireless transfer, and assist with avoiding channel interference. All of this is precluded with having a WiFi 7 Client STA and Access-Point which is capable of Wi-Fi 7, and which can behave as a Multi-Link Device, a device which is capable of taking advantage of MLO. 
     There are two operating modes in MLO – Simultaneous Transmit and Receive operation. (STR) and Non-Simultaneous Transmit and Receive (NSTR) Not too creative with the titling there but at least they’re easy to remember right?

     I’ve created two simple diagrams below to visualize the differences. In essence, STR allow the client STA to transmit on one-band while the other band serves to receive frames from the AP STA. This allows one band, perhaps the band which is able to achieve better throughput, to handle the majority of typical client traffic (downlink) in 5GHz, while the 2.4 GHz link services the  uplink traffic towards the AP. This happens simultaneously which is in direct contrast to NSTR Operation mode. 

###### MLO Operation Mode | STR 

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2024/06/wi-fi7-mlo.png?w=1024)

    The below diagram depicts NSTR, the non-simultaneous iteration of MLO. In this implementation, the STAs are using both radios and bands to send Uplink, or Downlink traffic at the same time. The next segment of time may be used to send Downlink, but the use of both bands is focused on one task or the other; transmitting or receiving traffic with faster  realized throughput rates.

###### MLO Operation mode | nstr

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2024/06/wi-fi7-mlo-nstr.png?w=1024)

    To close off this discussion for now (do realize, this is not holistic but instead intends only to highlight to core functionalities of MLO) I want to bring attention to some of the potential use-cases for the two modes above. 

    You can imagine that there are numerous permutations of how a MLD-capable AP STA could utilize either STR or NSTR modes. For example, the secondary link may be utilized to wake a power-save station to indicate it prepare for traffic on the other, active link. The AP may also use the MLO link to service more than 1 client simultaneously, dedicating one link to a particular client IP mapping and the other to another. 

    Depending on client STA and AP STA capabilities, more radios, more bands, and additional use cases may be made feasible. This post if fairly brief but make no mistake that MLO is, in my humble opinion, one of the more interesting additions to 802.11 that we have seen in recent amendments. Thanks for reading and apologies for the horrible delay in my posts recently, I’ve certainly been busy offline 🙂