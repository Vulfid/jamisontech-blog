---
title: "Roaming Over the Air | 802.11r"
date: 2022-05-14
tags: ["Wi-Fi", "802.11", "wireless", "roaming"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

May 14, 2022

**802.11r – Ratified May 2008**

“Fast Roaming”

**Alternative Industry Names: **Fast Transition, Fast Roaming, Fast Secure Roaming

**              **I would begin this post by first describing the behavior of a client STA, with no particular roaming mechanism at play, which has just decided that it’s observed RSSI from an AP has become too weak and it must now roam to a new AP. That client STA must first find a new AP within range and preferably of the same SSID. After having done so the client will have to perform the full 802.11 authentication, association, EAP-handshake (dependent upon EAP-type in-use), 4-way handshake, and finally begin its data traffic again. For applications which are latency sensitive, this whole exchange process just to begin communication-proper is far too long. When servicing voice applications over wireless you will want to aim for roam times of 50ms or less. But how to accomplish this? 

              Prior to 802.11r there were some other attempts at shortening roaming times for client STAs. Pre-authentication and PMK caching, for example, both did effectively lessen roam times but with some niches and limitations. 

**PMK caching** worked by having STAs ‘cache’ the PMKs generated from an 802.11 association, so that a client STA roaming back to previously associated STAs could skip the process of generating the PMK and continue where they left off using the old PMK. This, however, obviously only worked in the rear-view mirror, where a client STA had already connected to each given AP where PMK caching was at play. For a new client STA arriving at an office while on a voice call, this did nothing to lessen roam times. 

**Pre-authentication** has its own limitation as well. Pre-authentication would attempt to create RSNAs between a given client STA and other AP STAs which were part of the same service set. This meant that the client STA could skip the EAP-process when roaming as it already had an existing PMKSA with any AP in the BSS. This was a costly process however and didn’t scale very well in larger enterprise deployments where implementation of this could mean pre-authenticating with some hundreds of APs, which the client device might never actually connect to.

              Finally, we have 802.11r – Fast Securing Roaming. In 802.11r, APs which are part of the same ‘Mobility Domain’ are indicated in probe response and reassociation frames under a mobility domain element. With this mobility domain at hand, a client STA will roam to another AP within the same mobility domain in 1 of 2 ways: Over-the-Air or Over-the-DS. With Over-the-Air, the client STA communicates directly with the AP which it intends to roam, using the Fast Transition Re-association exchange to skip a repeat of the 802.1X/EAP exchange and get right to the 4-way handshake. In the case of Over-the-DS, the client STA communicates with the intended AP through its existing connection to the current AP; it then forwards the re-association requests over the wire to the next AP in the mobility domain. Both result in significant improvements to 802.11 roaming and better service of latency sensitive applications (Carpenter et al., 2021, p. 305-307).