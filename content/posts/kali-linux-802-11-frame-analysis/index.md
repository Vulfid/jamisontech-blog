---
title: "Kali-Linux | 802.11 Frame Analysis [Part 1]"
date: 2022-06-15
tags: ["Wi-Fi", "802.11", "wireless", "Kali Linux", "frame-analysis"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: false
---

June 15, 2022

You’ve probably heard, but for those just getting in to penetration-testing as a discipline – It’s rather well-known that Kali-Linux is an extremely versatile environment with a litany of free tools for numerous forms of hacking. It’s use for those interested in 802.11 Frame Analysis is just so.

This post won’t serve as a startup guide for spinning up VMware running Kali-Linux – For that Ill point you here: [https://www.instructables.com/Hack-wifi-/](https://www.instructables.com/Hack-wifi-/)

I run Kali in a VMware instance on my  laptop and perform captures with an ALFA [AWUS1900](https://www.alfa.com.tw/collections/kali-linux-compatible/products/awus1900) adapter (You can also see a list of Kali compatible adapters on the ALFA site, as well as, a guide on installing the relevant drivers for each).

Note that the AWUS1900 is an 802.11ac adapter, so it will not be able to de-modulate any observed transmissions which are 802.11ax. At the time of this writing the adapters for protocol analysis which are capable of 802.11ax analysis are prohibitively expensive for a young professional such as myself. 🙃

You’ll probably find the driver installation is the most difficult part of getting started with Kali.

![](https://guide-images.cdn.ifixit.com/igi/TiufGNbhhSwJPAoh.large)Kali Dragon

## Using Kali for 802.11 Protocol Analysis

With VMware running and your adapter running Kali-compatible drivers you are ready to begin using the tool for frame analysis!

You’ll be need to first utilize the airmon-ng suite to assure that your adapter is in monitor mode and NOT servicing as a typical Wireless connection for your PC. 

| $ sudo airmon-ng 

(will show a list of current adapter connected to your machine which can run in monitor-mode) 

From there, set the adapter into monitor-mode with

| $ sudo airmon-ng start wlan0 

With the adapter ready to monitor any and all 802.11 traffic it can hear, its time to start recording.

| $ sudo airodump-ng & wireshark

This previous command will start airodump-ng, capturing all oberserved wireless traffic (by default it will hop between 2.4GHz channels, so if a particular channel is being utilized by your AP STA that you want to analyze, specify that channel in the argument to airodump-ng). The modifier “wireshark” will display the captured frames in wireshark; Which is simply much easier on the eyes than airodump-ng’s default output on CLI.

From here your quest becomes one to learn how best to utilize wireshark. Will you set capture filters? Will you capture all the frames and filter them after? Are you capturing on the right channel? What are you troubleshooting? If its roaming then you should be aware of course that you’ll need a second adapter at play: One adapter in the channel of the current AP the client STA is connected to, and another in the target channel  of the next AP to be roamed to. Of course, you’ll also need to know enough about 802.11 frame formats (and the roaming solution at play) to correctly interpret and identify misconfiguration or errors in capture results. 

Let’s look quickly at one of my first captures performed using the setup I’ve outlined: Observing association to a WPA2-Personal SSID, broadcasted by a RUCKUS R310 AP (802.11ac), managed by a Cloud controller, and connecting with a Samsung Galaxy S20 smartphone. Note that the S20 is a 802.11ax capable client, but we will be able to see the full association and exchange (and even decrypt the data frames transferred) because the AP STA is 802.11ac and thus the Client will utilize 802.11ac the PHY.

![](https://jamisonwireless.wordpress.com/wp-content/uploads/2022/07/image-1.png?w=1024)

Above, I’ve filtered on only captured frames which include the MAC address of my Galaxy S20 phone (which I pre-emptively made static, be aware of random MAC addresses especially for mobile devices). You’ll notice we can see the whole 802.11 discovery and association process, as well as a surprising number of re-transmitted EAPoL Frame 1. 

In a short while I will be writing another blog diving more into some proper frame analysis and testing various 802.11 Security mechanisms – so stay tuned!