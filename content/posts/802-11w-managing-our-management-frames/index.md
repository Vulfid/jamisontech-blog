---
title: "802.11w | Managing our Management Frames"
date: 2022-05-05
tags: ["Wi-Fi", "802.11", "wireless", "security"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

May 5, 2022

**802.11w – Ratified October 2009**

“Protected Management Frames”

**Alternative Industry Names: **Robust Management Frames, PMF, Management Frame Protection (MFP)

To this day, it is possible to forcibly de-authenticate a given STA from its legitimate AP and force it to re-authentication with a rogue AP to conduct a man-in-the-middle attack. The abuse and spoofing of a few particular management frames were problematic enough to warrant the development of this amendment. 802.11w protects critical management frames like the disassociation frame to, in no small part, protect against just these attacks. 802.11w is supported in WPA 2 certified STAs but will not become a required feature until WPA 3, which itself is finally seeing wider deployment at the time of writing.

              Recall that much of the security of the wireless medium has left the management and control aspects entirely unguarded. While data frames are encrypted via whichever encryption scheme might be at play, certain key aspects of the protocol at the MAC layer are left un-protected. In example, the MAC addresses in 802.11 frames are never encrypted nor are management frames, as they must be heard and understood by all clients to properly respond to and reserve the medium. Rather than encryption, 802.11w seeks to provide some assurance of legitimacy and authentication behind 802.11 management frames.

               A number of management frames cannot be protected – those which occur before the 4-way handshake. This leaves beacons, probe req/resp, ATIM, authentication, association req/resp, and spectrum management actions unguarded. Once the 4-way handshake is complete, key management frames can be protected. This list includes disassociation frames, deauthentication, action frames (block ACK req/resp), channel switch announcements, etc. (IEEE 802.11w-2009, 2009).