---
title: "802.11i | Security in the air tonight!"
date: 2022-04-30
tags: ["Wi-Fi", "802.11", "wireless", "security"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

April 30, 2022

**802.11i – Ratified May 2004**

**“**MAC Security Enhancements”

**Alternative Industry Names: **Robust Security Network (RSN)/RSN Association (RSNA), Wi-Fi Protected Access II (WPA 2)

**              **I had introduced you to 802.11i initially without explaining what led to this amendment. To contextualize this amendment, you will need to know a few things. Prior to 802.11i, 802.11-Prime allowed only for the use of Wired Equivalency Protocol (WEP) in securing wireless communications. WEP utilized a 40-bit key to protect wireless traffic from simple eavesdropping. A 24-bit Initialization-Vector (IV) was used in the encryption/decryption process. In the combined 64-bit “WEP Key” the problem was the shortness of the IV allowed for capturing of enough frames to effectively decode the traffic. Even when a 104-bit key with a 24-bit IV became an option, several prolific Wi-Fi hacks lead to the need and finally the development of an improvement in 2004 – 802.11i. (Badman et al., 2019, p. 3)

The ratification of 802.11i would introduce a still-relevant term to Wi-Fi, that being the concept of a Robust Security Network (RSN) or Robust Security Network Association (RSNA). Simply put, in 802.11 an RSNA is any association between two STAs that is the result of a 4-way handshake. In 802.11, an STA “Station” is defined as a “logical entity that is a singly addressable instance of a medium access control (MAC) and physical layer (PHY) interface to the wireless medium.” (“IEEE 802.11-2020”, 2020). Where legacy WEP of 802.11-Prime would utilize Shared Key Authentication, an RSNA would not allow use of that legacy method, instead solely allowing devices capable of 802.11 Open System Authentication. You might also hear the term *“Pre-RSNA”*, an advent which came along with 802.11-2012 and meant that a compliant STA would be capable of servicing both RSNA-only clientele and Pre-RSNA clients as well (Badman et al., 2019, p. 4).

Wherein the original encryption for 802.11 was WEP, temporarily enhanced by TKIP implementation with the original WPA certification, 802.11i introduced support for Advanced Encryption Standard (AES) along with Country Mode CBC-MAC Protocol (CCMP). This marked the beginning of WPA2 certification through the Wi-Fi Alliance. Do note that WPA2 still has support for TKIP authentication (though it certainly isn’t advised). As it stands today, WEP/TKIP is a deprecated implementation which should be avoided in most deployments.

Works Cited

Badman, L., Bartz, R., Carpenter, T., Hill, B., & Morgan, P. (2019). *CWSP-206 Certified Wireless Security Professional Official Study and Reference  Guide* (1st ed.). CertiTrek Publishing.

“IEEE Standard for Information technology – Telecommunications and information exchange between systems – Local and metropolitan area networks – Specific requirements. Part 11: Wireless LAN Medium Access Control (MAC) and Physical Layer (PHY) Specifications Amendment 4: Protected Management Frames,” in IEEE Std 802.11w-2009 (Amendment to IEEE Std 802.11-2007 as amended by IEEE Std 802.11k-2008, IEEE Std 802.11r-2008, and IEEE Std 802.11y-2008) , vol., no., pp.1-111, 30 Sept. 2009, doi: 10.1109/IEEESTD.2009.5278657.