---
title: "802.11 Major Amendments & Their Primary Features | From 802.11-Prime to .11be"
date: 2024-10-09
tags: ["Wi-Fi", "802.11", "wireless"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

October 9, 2024

In recently re-upping my CWDP by examination, I was remind of a personal project for general “at-a-glance” review I have been putting together for 802.11.  A general summation of high-level terms, technologies, and notable inclusion in each amendment. Some of this is pulled from my earliest blog posts when I was first studying for the CWAP and I’m sure those pursuing it would find this reference summation of some use. 

802.11 Major Amendments & their Primary Features

Introduction
Having only recently acquired my CWDP/AP/SP certifications from the CWNP, I’ve become heedful of the areas where further elaboration on significant aspects of the overall 802.11 protocol is crucial. What’s more, for someone just getting into the 802.11 protocol (or computer science topics in general) the content is likely to be a bit steeper of a learning curve and the vocabulary somewhat unfamiliar. For those reasons I wanted to put together this write-up which may serve as both a general introduction to the 802.11 Standard, it’s history, and the technologies which it defines. This will not be a comprehensive document as I personally am not capable of writing on some of the most complex aspects of the protocol. I will do my best here to refer to supplementary materials to fill in the gaps which both you and I discover in our journey to understand the complexities of these technologies.
Fun Fact: Both Wi-Fi and I were ratified in 1997. 802.11 is a 90s kid. 

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2024/12/image.png?w=553)

802.11-1997 (Prime) – Ratified June 1997
“IEEE Standard for Wireless LAN Medium Access Control (MAC) and Physical (PHY) Specifications.”
Alternative Industry Names: “802.11 Prime”, 802.11 Legacy Mode, 802.11 STANDARD (while others are amendments to the original standard that is 802.11 – 1997 or roll-ups of the standard like 802.11-2020)
PHY Acronym: Direct-Sequence Spread Spectrum (DSSS)
The original 802.11 Standard created by the IEEE. Very important note: This standard and all amendments to come include both the Data Link (MAC) layer and Physical (PHY) layer of the 802.11 protocol. The whole of the MAC + PHY state machine is outlined in the IEEE standards. This includes the sub-layers of each [Figure 11] (Carpenter et al., 2021, p. 150).

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2024/12/image-1.png?w=536)

Note that this is not the original ISO/IEC model, as this includes the RSNA Key management which we will see is not implemented until 802.11i in response to vulnerabilities observed in WEP & TKIP/WPA (Wi-Fi Alliance). It also features mention of the 802.1X roles served by client and AP STAs in an 802.1X association, this however is an entirely different IEEE standard which 802.11 utilizes and not a part of the 802.11 standard itself. What this model does show is the logical state machine outlined in the IEEE’s 802.11 standard. As you investigate the different functions of the 802.11 protocol in both the MAC and PHY, pay close attention to how data is handed down/up the stack and what is done at each sublayer (PLCP vs PMD for example, and their functions).
802.11 – 1997 allowed for three physical layer technologies:
⦁ Infrared at 1 Mbps data rates
⦁ Frequency-Hopping Spread-Spectrum (FHSS) at 1 Mbps and 2 Mbps data rates
⦁ Direct-Sequence Spread-Spectrum (DSSS) at 1 Mbps and 2 Mbps data rates
FHSS and DSSS use the unlicensed 2.4 GHz Industrial Scientific Medical (ISM) frequency band. DSSS allows for up to fourteen 22 MHz wide channels (three non-overlapping), while FHSS utilizes 79 non-overlapping 1 MHz wide channels.
802.11 was originally written for compliance in the United States, Japan, Canada, and Europe only. DSSS in 2.4GHz was the most widely used technology from this standard and for that reason it would serve you to recognize a few key details that make DSSS and its channel structure different. If you are familiar with modern 802.11ac/ax wireless networks in the enterprise, the 22 MHz wide channels used with DSSS in 802.11 – 1997 should stand out to you. The wider channel width meant that the side-band interference for which you and I know 2.4 GHz so well was even worse before the introduction of the OFDM channel sizes and technology with 802.11a (more on that to come).
[NOTE: For reference, a 22MHz wide channel, on Channel 1 with its center at 2.412 GHz for example, would use 11 MHz on either side of that center frequency for the transmission. Meaning Channel 1 ranged from 2.401 GHz up to 2.423 GHz.]
DSSS allowed for only a single spatial stream — SISO (Single-input/single-output). The transceiver (transmitter/receiver) sends one stream of data or receives one stream of data at a time. As we continue you will see the addition of (up to 8) spatial streams in Wi-Fi communications allows for significant improvements in available data rates. In fact, the relationship between the number of SS possible and data rate is a linear increase for each additional SS. All future 802.11 STAs still support DSSS PHY unless otherwise disabled by some compounding setting (like G-only or N-only modes on future AP STAs).
Bands Supported: 2.4 GHz (The ISM Band) [Channel 1 at 2.412 GHz – Channel 14 at 2.484 GHz] – ~100 MHz of Spectrum.
Data Rates Supported: 1, 2 Mbps (in 2.4 GHz)

## 802.11b – Ratified September 1999

“Higher Speed PHY Extension in the 2.4 GHz Band”
Alternative Industry Names: Wi-Fi 1
PHY Acronym: HR/DSSS, High Rate/Direction Sequence Spread Spectrum PHY
802.11b would introduce data rates of 5.5 Mbps and 11 Mbps to Wi-Fi. This was accomplished with two different alternatives to the modulating and coding schemes: Complementary Code Keying (CCK) or Packet Binary Convolutional Coding (PBCC). 802.11b is commonly referred to as High-Rate Direct-Sequence Spread-Spectrum (HR-DSSS).
[NOTE: As we cover all the different technologies that enabled higher speeds in 802.11 connections, I would advise you avoid a misconception I initially held for years: The data rate of the MAC frame is sent at the rate indicated, but the PHY information is sent at the lowest supported data rate in the BSS. I.E.: On a BSS where the agreed upon data rates between client STA and AP STA are 6 Mbps, 12 Mbps, etc., The MAC Frame itself will be transmitted (that is to say, the combined modulation and coding scheme used to transmit and receive the frame) at the highest data rate which the STAs can achieve. However, the PHY Preamble of all frames will be transmitted at the LOWEST data rate supported in the BSS. Even if you administratively disabled 6 Mbps as a basic data rate in an 802.11a network, the PHY would still be transmitted at 6 Mbps. Let me prepare you for one other knowledge-bomb: Management frames are not transmitted at the same data rate as data frames.]
Most of the increase in data rate is due to the introduction of a more capable encoding scheme, that of CCK. To contextualize the combinations of coding rates, guard intervals, spatial streams, and modulations I would recommend at least familiarizing yourself with the MCS index here: [https://mcsindex.com/](https://mcsindex.com/) you will need to refer to it fairly often to determine data rates when numerous combinations of technologies are at play.
Bands Supported: 2.4 GHz
Data Rates Supported: 5.5 Mbps, 11 Mbps (in 2.4 GHz)

NOTE: 802.11a marks the introduction of 5 GHz to Wi-Fi!

## 802.11a – Ratified September 1999

“Higher Speed PHY Extension in the 5 GHz Band”
Alternative Industry Names: Wi-Fi 2
PHY Acronym: Orthogonal Frequency Division Multiplexing (OFDM) PHY
Ratified in tandem with 802.11b, 802.11a allowed for data rates up to 54 Mbps in the 5 GHz U-NII band by utilizing Orthogonal Frequency Division Multiplexing (OFDM). 802.11a specified twelve non-overlapping 20 MHz wide channels in the Unlicensed National Information Infrastructure (U-NII) frequency bands.
[Note: This was the introduction of not just a new bandwidth to 802.11, but a new modulation scheme. OFDM would at this point only be available in the 5 GHz band, meaning that HR-DSSS/DSSS would still be present in 2.4 GHz and utilizing those 22 MHz wide DSSS channels, despite the introduction of OFDM’s 20 MHz wide channel structure in 802.11a– which persists to this day in 802.11ax. (With new additions of course)].
OFDM still utilizes a single spatial stream. However, due to the enhanced modulation in 802.11a, it is capable of supporting data rates of 6, 9, 12, 18, 24, 36, 48, and 54 Mbps. You’ll note that OFDM/802.11a does NOT support the previous data rates which 802.11b introduced into 2.4 GHz because this is a new band. From here on out (in 5 GHz at least) 6 Mbps will remain the lowest data rate you’ll see.
[A question for you: At what data rate would an 802.11n management frame be transmitted?] Answer at page end.

Bands Supported: 5 GHz
Data Rates Supported: 6, 9, 12, 18, 24, 36, 48, and 54 Mbps.

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2024/12/image-3.png?w=780)

For reference, this 802.11 spectral mask diagram shows the general shape of the signals. (Ng, S.W.K. & Szymanski, Ted., 2012). You can see how the width and shape of the spectrum changes with OFDM

## 802.11g – Ratified June 2003

“Further Higher Data Rate Extension in the 2.4 GHz Band”
Alternative Industry Names: Wi-Fi 3, ERP
PHY Acronym: Extended Rate PHY (ERP) (802.11g).
802.11g introduced the Orthogonal Frequency Division Multiplexing (OFDM) transmission mechanisms into the 2.4 GHz band. The PHY reverts to Complementary Code Keying (CCK) to achieve 5.5 Mbps and Differential Binary Phase-Shift Keying (DBPSK)/Differential Quadrature Phase-Shift Keying (DQPSK)/Direct-Sequence Spread-Spectrum (DSSS) for 1 Mbps and 2 Mbps data rates. 802.11g also utilizes 20 MHz wide channels and is backward compatible with the previous DSSS based protocols. This is different from 5 GHz in that 2.4 GHz Wi-Fi STAs might utilize 22 MHz or 20 MHz channels but will soly have access to 20 MHz OFDM channels in 5 GHz (at least until bonded/wider channels are introduced in 802.11n). (Carpenter et al., 2021, p. 23)
Bands Supported: 2.4 GHz
Data Rates Supported: 6, 9, 12, 18, 24, 36, 48, and 54 Mbps. While still supporting 802.11-Prime/b data rates.

## 802.11i – Ratified May 2004

“MAC Security Enhancements”
Alternative Industry Names: Robust Security Network (RSN)/RSN Association (RSNA), Wi-Fi Protected Access II (WPA 2)
I had introduced you to 802.11i initially without explaining what led to this amendment. To contextualize this amendment, you will need to know a few things. Prior to 802.11i, 802.11-Prime allowed only for the use of Wired Equivalency Protocol (WEP) in securing wireless communications. WEP utilized a 40-bit key to protect wireless traffic from simple eavesdropping. A 24-bit Initialization-Vector (IV) was used in the encryption/decryption process. In the combined 64-bit “WEP Key” the problem was the shortness of the IV allowed for capturing of enough frames to effectively decode the traffic. Even when a 104-bit key with a 24-bit IV became an option, several prolific Wi-Fi hacks lead to the need and finally the development of an improvement in 2004 – 802.11i. (Badman et al., 2019, p. 3)
The ratification of 802.11i would introduce a still-relevant term to Wi-Fi, that being the concept of a Robust Security Network (RSN) or Robust Security Network Association (RSNA). Simply put, in 802.11 an RSNA is any association between two STAs that is the result of a 4-way handshake. In 802.11, an STA “Station” is defined as a “logical entity that is a singly addressable instance of a medium access control (MAC) and physical layer (PHY) interface to the wireless medium.” (“IEEE 802.11-2020”, 2020). Where legacy WEP of 802.11-Prime would utilize Shared Key Authentication, an RSNA would not allow use of that legacy method, instead solely allowing devices capable of 802.11 Open System Authentication. You might also hear the term “Pre-RSNA”, an advent which came along with 802.11-2012 and meant that a compliant STA would be capable of servicing both RSNA-only clientele and Pre-RSNA clients as well (Badman et al., 2019, p. 4).
Wherein the original encryption for 802.11 was WEP, temporarily enhanced by TKIP implementation with the original WPA certification, 802.11i introduced support for Advanced Encryption Standard (AES) along with Country Mode CBC-MAC Protocol (CCMP). This marked the beginning of WPA2 certification through the Wi-Fi Alliance. Do note that WPA2 still has support for TKIP authentication (though it certainly isn’t advised). As it stands today, WEP/TKIP is a deprecated implementation which should be avoided in most deployments.

## 802.11e – Ratified September 2005

“MAC Enhancements QoS”
802.11e would extend QoS service into the wireless medium through added functionality at the MAC layer. This was accomplished through prioritization of frames based upon traffic class. Originally, 802.11 relied upon Distributed Coordination Function (DCF) which itself relied upon Carrier-Sense Multiple Access/Collision Avoidance (CSMA/CA) to minimize collisions occurring over-the-air. CSMA in turn would utilize two significant steps in its avoidance of collisions: Carrier-Sense (CS) and Energy-Detect (ED). “Carrier-Sense is the process of checking to see if the medium is in use or busy” (Carpenter et al., 2021, p. 327). Now, we’re continuing down the ‘nested’ rabbit hole because Carrier-Sense itself was performed in two different varieties for 802.11 WLANs: Virtual Carrier-Sense and Physical Carrier-Sense. Physical CS utilizes Clear Channel Assessment (CCA) to discover whether the physical medium is in use and is thusly provided by the PHY to the MAC layer of the 802.11 protocol, whereas Virtual CS utilizes a Network Allocation Vector (NAV), provided by the MAC, which sets a timer for how long an STA must wait before attempting to transmit after observing a competing STA’s frame duration field value.
Between detecting the presence of energy on the RF medium and responding appropriately to reported transmission intervals observed in 802.11 frames, I believe that the coordination functions are some of the most interesting aspects of the protocol and would advise that you seek out more detailed writings on how that technology conducts itself. It’s a wonder any of this stuff works!
Getting back on topic, 802.11e would better the existing DCF (and the seldom-implemented PCF) by means of the Hybrid Coordination Function (HCF). HCF itself contained two methods of channel access: HCF Controlled Channel Access (HCCA) and Enhanced Distributed Channel Access (EDCA). Present in both is the concept of traffic categories. The 4 defined traffic/Access Categories (AC) as follows:
⦁ Background (AC_BK)
⦁ Best Effort (AC_BE)
⦁ Video (AC_VI)
⦁ Voice (AC_VO)
Each of the Access Categories defined in EDCA maps to a particular CWmin, a minimum contention window which determines a minimum value that each traffic category must wait before attempting to access the medium. When a transmission is sent and fails to be acknowledged, that frame’s CWmin is doubled, +1. This increase will occur each time the frame must be re-transmitted until the maximum window is reached and eventually the frame abandoned after 32 re-transmission attempts. Below is a mapping of Access Categories to CWminimums/maximums for 802.11a/n. (“IEEE 802.11e-2005, Wikipedia”, 2022)

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2024/12/image-4.png?w=559)

One thing noted in this table but not yet written about here is Arbitrary Inter Frame Spacing Number (AIFSN) which is calculated as
For each PHY there is a standardized SIFS time value AND a separate per-PHY slot-time value. The space between every transmitted frame in an 802.11 exchange is dependent upon which Interframe-Spacing type is defined to follow that frame sub-class.
We’ve now defined AIFSN and mentioned that SIFS is defined per-PHY and used in the calculation of other Interframe-Spacing (IFS) values. SIFS stands for short-interframe-spacing and, like slot-time, is utilized in the calculation of the other interframe-spacing timings. The effect of this is that a particular PHY with a shorter-defined SIFS value/slot-time might have access to the medium slightly before another PHY.
NOTE: I would refer you to an external resource to study these interframe spacing types in more detail (Pindoria, 2017). Additionally, the CWAP text itself does a great job explaining interframe spacing. If you’re craving a mnemonic to remember the interframe-spacing names and their order from shortest to longest: RSP DAE “Raspy Day” – when said aloud.

## 802.11k – Ratified May 2008

“Radio Resource Measurement of Wireless LANs”
Alternative Industry Names: Assisted Roaming
802.11k and its neighbor reports will provide a distinctly critical service in enabling the faster, secure roaming of 802.11r. The neighbor reports provided by 802.11k to a requesting client STA allow that STA to make informed decisions regarding which nearby APs it might roam to before it becomes necessary. A client STA will send an Action Management Frame to an AP STA requesting this neighbor report, which the AP will supply in a unicasted Action Management Frame. This provides that client STA with that list of potential roaming candidates. 802.11k neighbor reports include a ‘reachability’ value for the given BSSID. Capture a Neighbor Report Response frame and you can see these values for yourself in the “BSSID Information” subfield.

## 802.11r – Ratified May 2008

“Fast Roaming”
Alternative Industry Names: Fast Transition, Fast Roaming, Fast Secure Roaming
I would begin this section by first describing the behavior of a client STA, with no particular roaming mechanism at play, which has just decided that its observed RSSI from an AP has become too weak and it must now roam to a new AP. That client STA must first find a new AP within range and preferably of the same SSID. After having done so the client will have to perform the full 802.11 authentication, association, EAP-handshake (dependent upon EAP-type in-use), 4-way handshake, and finally begin its data traffic again. For applications which are latency sensitive, this whole exchange process just to begin communication-proper is far too long. When servicing voice applications over wireless you will want to aim for roam times of 50ms or less. But how to accomplish this?
Prior to 802.11r there were some other attempts at shortening roaming times for client STAs. Pre-authentication and PMK caching, for example, both did effectively lessen roam times but with some niches and limitations.
PMK caching worked by having STAs ‘cache’ the PMKs generated from an 802.11 association, so that a client STA roaming back to previously associated STAs could skip the process of generating the PMK and continue where they left off using the old PMK. This, however, obviously only worked in the rear-view mirror, where a client STA had already connected to each given AP where PMK caching was at play. For a new client STA arriving at an office while on a voice call, this did nothing to lessen roam times.
Pre-authentication has its own limitation as well. Pre-authentication would attempt to create RSNAs between a given client STA and other AP STAs which were part of the same service set. This meant that the client STA could skip the EAP-process when roaming as it already had an existing PMKSA with any AP in the BSS. This was a costly process however and didn’t scale very well in larger enterprise deployments where implementation of this could mean pre-authenticating with some hundreds of APs, which the client device might never actually connect to.
Finally, we have 802.11r – Fast Securing Roaming. In 802.11r, APs which are part of the same ‘Mobility Domain’ are indicated in probe response and reassociation frames under a mobility domain element. With this mobility domain at hand, a client STA will roam to another AP within the same mobility domain in 1 of 2 ways: Over-the-Air or Over-the-DS. With Over-the-Air, the client STA communicates directly with the AP which it intends to roam, using the Fast Transition Re-association exchange to skip a repeat of the 802.1X/EAP exchange and get right to the 4-way handshake. In the case of Over-the-DS, the client STA communicates with the intended AP through its existing connection to the current AP; it then forwards the re-association requests over the wire to the next AP in the mobility domain. Both result in significant improvements to 802.11 roaming and better service of latency sensitive applications (Carpenter et al., 2021, p. 305-307).

## 802.11n – Ratified September 2009

“High Throughput”
Alternative Industry Names: Wi-Fi 4
You’ve heard of 20 MHz channels – now get ready for something cooler: 40 MHz!
With 802.11n, Wi-Fi would see the introduction of its first wide channel with 40 MHz. An 802.11n AP, which is broadcasting with a 40 MHz channel, can still service clients which are not capable of utilizing the bonded 40 MHz channel by simply communicating with those clients solely in the primary 20 MHz channel. This primary + secondary channel structure and naming scheme will come up often so familiarize yourself with the concept that the primary channel will be on one of those 20 MHz channels (channel 36 for example) while the secondary channel will be either +1 or -1, meaning it will be the 20 MHz channel, one below or one above, the primary. Realize that while 802.11n introduced these bonded 40 MHz channels to both 2.4 and 5 GHz, it is not advisable to utilize anything beyond 20 MHz channels in 2.4 GHz as there is simply no room in the spectrum for multiple APs to utilize 40 MHz channels which would overlap and create even more contention in 2.4 GHz.
Unsurprisingly, wider channels mean an increase in possible data rates even with no other additions to the PHY. Luckily, we also have those! The HT PHY included the addition of a new technology to 802.11 — Multiple Spatial Streams in the form of Multiple Input/Multiple Output (MIMO). HT PHY allows for up to 4 concurrent spatial streams of data which are combined at the receiver into a greater aggregate throughput. This is where you’ll see enterprise AP models list the number of“R x T : SS” they are capable of: i.e. . Given the introduction of wider channels, MIMO, and the modulation schemes at play, 802.11n is capable of some impressive maximum data rates that peak at 600 Mbps with 4 Spatial Streams on a 40 MHz channel. Of course, the client STA and AP STA must both be possible of 4 SS for this rate to be achieved. At the time of 802.11n most were capped at 3 SS which meant a linear decrease to a ‘meager’ 450 Mbps. In other words, 150 Mbps per SS when a HT STA is using a 40 MHz channel. This linear relationship between maximum data rate and the number of spatial streams will continue into later amendments.
[NOTE: At this point, the MCS Index becomes a bit of a necessity due to how many different possible combinations of technologies result in distinct data rates.]
Bands Supported: 2.4 & 5 GHz
Data Rates Supported: ranged from 72.2 Mbps with a 1×1 STA on a 20 MHz channel in 2.4 GHz up to 600 Mbps with a 4×4 STA on a 40 MHz channel in 5 GHz.

## 802.11w – Ratified October 2009

“Protected Management Frames”
Alternative Industry Names: Robust Management Frames, PMF, Management Frame Protection (MFP)
To this day, it is possible to forcibly de-authenticate a given STA from its legitimate AP and force it to re-authentication with a rogue AP to conduct a man-in-the-middle attack. The abuse and spoofing of a few particular management frames were problematic enough to warrant the development of this amendment. 802.11w protects critical management frames like the disassociation frame to, in no small part, protect against just these attacks. 802.11w is supported in WPA 2 certified STAs but will not become a required feature until WPA 3, which itself is finally seeing wider deployment at the time of writing.
Recall that much of the security of the wireless medium has left the management and control aspects entirely unguarded. While data frames are encrypted via whichever encryption scheme might be at play, certain key aspects of the protocol at the MAC layer are left un-protected. In example, the MAC addresses in 802.11 frames are never encrypted nor are management frames, as they must be heard and understood by all clients to properly respond to and reserve the medium. Rather than encryption, 802.11w seeks to provide some assurance of legitimacy and authentication behind 802.11 management frames.
A number of management frames cannot be protected – those which occur before the 4-way handshake. This leaves beacons, probe req/resp, ATIM, authentication, association req/resp, and spectrum management actions unguarded. Once the 4-way handshake is complete, key management frames can be protected. This list includes disassociation frames, deauthentication, action frames (block ACK req/resp), channel switch announcements, etc. (IEEE 802.11w-2009, 2009).

## 802.11ac – Ratified December 2013

“Very High Throughput 5GHz”
Alternative Industry Names: Wi-Fi 5, VHT
PHY Acronym: Very High Throughput (VHT) (802.11ac).
802.11ac is significant for its introduction of several improvements to the 5GHz ISM band -increasing throughput to 6.9 Gbps. 802.11ac defined support for 80 MHz and 160 MHz wide channels, (though note the support for 80+80 bonded channels and 160 MHz channels in 802.11ac is OPTIONAL unlike most other features). The base channel of these yet-wider channels is still 20 MHz, but a combination of two to eight 20 MHz channels is utilized in forming these wider channels. A wider channel enables higher data rates, however, 802.11ac also defines support for additionally concurrent spatial streams, now up to 8. As before with 802.11n, realize that the peak spatial stream count enabled by the standard does not reflect industry support as at the time near every device that was 802.11ac compatible only supported up to 4 spatial streams if that.
VHT devices can realize data rates up to 6,933.3 Mbps with 8 spatial streams and an ideal environment. The realistic limit for any single client would be 3,466.7 Mbps with 4 spatial streams. Again, this all requires a 160 MHz channel as well. A pipedream in near any enterprise deployment. You ought to consider 40 MHz channels as an ideal maximum in reality when configuring .11ac and beyond APs – realizing a peak of 800 Mbps (Carpenter et al., 2021, p. 24-27).
Additionally, 256-QAM Modulation provided some ~25% higher data rate with .11ac’s max of 8 Spatial Streams. Multi-User – Multiple Input Multiple Output (MU-MIMO) and improvements applied to existing beamforming technologies (these being the implementation of NDP and changes to the beamformee role and reporting methodology defined previously in 802.11n). Most critically, these changes to the beamforming technology attempted to tackle interoperability issues with beamforming, which saw limited success when communicating between different vendors technologies in 802.11n.
Bands Supported: 5 GHz
Data Rates Supported: Up to 6,933.3 Mbps (6.9 Gbps) in 5 Ghz on a 160 MHz channel with 256-QAM and 8 concurrent spatial streams on both the client STA and AP STA.

## 802.11ax – Ratified February 2021

NOTE: 802.11ax marks the introduction of 6 GHz to Wi-Fi!

“High-Efficiency WLAN”
Alternative Industry Names: Wi-Fi 6, Wi-Fi 6E
PHY Acronym: High-Efficiency (HE) (802.11ax).
802.11ax devices operate in both 2.4 & 5 GHz bands while also extending support to the 6GHz, – allocating some 59 channels across 1200 MHz of added spectrum. The additional support for the 6 GHz band is often referred to as “Wi-Fi 6E” by the industry. You’re likely seeing at the time of writing that Wi-Fi 6E AP models are just hitting the market, while “Wi-Fi 6” models have been out for over a year. What remains to be seen is how quickly client devices will implement support for 6 GHz or if we will be waiting around for Wi-Fi 7 before we can utilize this massive addition of spectrum.
Of particular note, in 802.11ax, is the introduction of Orthogonal Frequency-Division Multiple Access (OFDMA) – allowing the subdivision of a channel into smaller frequency allocations called Resource Units (RUs). OFDMA has allowed APs to transmit small sections of data to multiple users simultaneously: This is a significant improvement in efficient use of the medium. One of the aspects of 802.11 and wireless communications in general, which has been so difficult for me to grasp myself, is simply that these technologies are not necessarily invented just for 802.11. OFDMA has been utilized and seen success already in other wireless communication protocols and is just now seeing the light of day in the IEEE’s 802.11 standard. As a rudimentary explanation, OFDMA sub-divides the channel into numerous resource units (RU), or, sub-channels. These RUs can be utilized to transmit or receive between multiple STAs concurrently. Personally, the fact that something like this works at all boggles the mind. I would advise you contact your local PhD in RF and Communication theory for a deeper explanation. (Carpenter et al., 2021, p. 27-30)
Do also note that while 802.11ac initially defined downlink MU-MIMO, 802.11ax brings uplink MU-MIMO to Wi-Fi as well.
Additionally, 802.11ax saw the introduction of technologies like BSS Coloring, Target Wake Time, and 1024-QAM Modulation Coding. With a 160 MHz channel, 1024-QAM, and 8 spatial streams, the amendment would allow a maximum achievable data rate of 9.6 Gbps.
Bands Supported: 2.4 GHz, 5 GHz, and 6 GHz
Data Rates Supported: Up to 9.6 Gbps in 5 GHz, 6 GHz.

## 802.11be – Ratified TBA – Expected final draft by early 2024

NOTE: I wrote this section in what must have been late 2022? I will leave it as a time capsule 😊See my or other industry professionals discussion on Wi-Fi 7 which cover it’s core features and functionalities in more detail. Wi-Fi 7 APs are sure marching on the market now.

“Extremely High Throughput (EHT)”
Alternative Industry Names: Wi-Fi 7
Albeit still a draft, 802.11be promises to be the next generation in 802.11. It builds upon what we’ve seen introduced in 802.11ax with features such as 320 MHz-wide channels, 16 Spatial Streams, enhancements to the MIMO protocol itself, multi-AP coordination, enhanced link adaptation and retransmission protocol, 4096-QAM (4K-QAM), and support for client direct links (Verney, 2022).
Honestly, how much higher can all these numbers go? It is expected that 802.11be will be ratified by late 2024. That remains to be seen, but of course, we will be waiting to see how quickly (and widely) these technologies are adopted by both enterprise and consumer manufacturers. You can see the official IEEE working group schedules and timeline here: [https://www.ieee802.org/11/Reports/802.11_Timelines.htm](https://www.ieee802.org/11/Reports/802.11_Timelines.htm)
Bands Supported: 2.4/5/6 GHz
Data Rates Supported: Up to 40 Gbps

## Closing Notes – Hunter R. Jamison

In writing this surface-level introduction of 802.11 and its primary amendments I’ve been reminded just how daunting the scale of these technologies are. There are specialists of many varieties behind these technologies and an industry full of people with many years of experience that still don’t have a full explanation of how and why every feature may or may not exist in any given implementation of the standard. Intimidating to say the least.
Recently I spoke with a much more tenured member of the wireless community who told me a story of someone who works on writing implementations of the 802.11 protocol. This implementer, no doubt writing the embedded firmware that goes into some vendor’s products, was familiar with the countless aspects of 802.11 and RF theory in general to a level of detail which I assume is only achieved through tireless years of study. In particular, they spoke of Point-Coordination Function in great detail but when a particular problem had come into conversation, the brilliant implementer proposed the use of PCF. But the reality is that PCF, despite being in the standard for some time and only recently deprecated, has never really been adopted by any vendors. This story stuck out with me as someone who eventually seeks to have a hand in creating implementations of protocols like 802.11. To know theory in great detail is invaluable, but you ought to keep yourself familiar with the realities of the industry as well.
I hope this write-up will service some in beginning their familiarity with not just the 802.11 protocol, but with the industry and its state at this time.