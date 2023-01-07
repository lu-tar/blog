---
title: "Wi-Fi channels"
date: 2022-12-26T16:13:53+02:00
draft: false
tags: ["wireless", "channel", "PHY", "Channelyzer"]
categories: ["in-depth"]
cover:
  image: img/channels.png
  alt: ''
  caption: ''
ShowToc: true
author: ["Luca"]
---
# Wi-Fi channels

![/gif/old-tv.gif]

This article is an overview of what I've learned in the past few years about Wi-fi channels and the topics floating around this key concept of wireless telecommunication.
The external resources I've used are in the bottom of the page.

## What is a wireless channel? 
Before addressing the individual topics, this is mine definition of a wireless channel based on what I've learned so far: 

>a channel is a numbered, shared, layer 1 domain with a specific bandwidth (often expressed in MHz) and a center frequency.

Let's unpack the little, over condensed, definition of channel:
- A RF channel is **numbered** because for sure is easy to remember channel "1" instead of a "22 MHz channel with center frequency of 2412 MHz between 2401 and 2423 MHz".
- A RF channel is a **shared** medium because, unlike IEEE 802.3 Ethernet which is protected from the outside, there is no "a shielded twisted pair" over the air and interferers and ground noise are variables to be strongly taken into consideration.
- A RF channel is a **layer 1 domain** because it sits alongside 802.3 in the bottom of the ISO-OSI pile.
- A Wi-fi RF channel uses **bandwidth** of 20 MHz for OFDM-based trasmissions and 22 MHz for DSSS-based, but we can bond channel together and double the bandwidth up to 160 MHz. 20, 40, 80 and 160 MHz are the wireless enterprise standards but some technologies can use 1 (like Bluetooth), 5 or 10 MHz bandwidth.
- The **center frequency** of a RF channel is the peak frequency where most of the information are transmitted.

This definition can surely be expanded according to your background but for a networking engineer with a mixed knowledge of routing and switching and wireless is a good starting point. 

In the next section I'm going to unpack different concepts around the "wireless channel":
1. OFDM vs DSSS encoding
2. Channel bonding
3. Good and bad channel utilization, The Shannon limit
4. Co-channel interference
5. Channel assignment algorithms: Cisco DCA

### OFDM vs DSSS encoding scheme or "why a Wi-Fi channel has this shape?"

802.11g – ERP-OFDM // 802.11a – OFDM vs 802.11b – HR/DSSS

> The 802.11 standard specifies a spectral mask which defines the permitted power distribution across each channel. The spectral mask requires the signal be attenuated to certain levels (from its peak amplitude) at specified frequency offsets. The spectral mask used for the 802.11b standard. While the energy falls off very quickly from its peak, it is interesting to note the RF energy that is still being radiated at other channels.
![[Pasted image 20230107011439.png]]
![[channels.png]]

> For the 802.11a, 802.11g, 802.11n and 802.11ac standards that are using the OFDM encoding scheme, they have a spectral mask that looks entirely different. OFDM allows for a more dense spectral efficiency, thus it gets higher data throughput than used with the BPSK/QPSK techniques in 802.11b

![[Pasted image 20230107011514.png]]
![[channel_square.png]]

### Channel bonding or "why I need to be careful with channel bandwidth"
[# Mama Says Channel Bonding is the Devil by Nick Shoemaker](https://blogs.arubanetworks.com/solutions/mama-says-channel-bonding-is-the-devil/)

### The Good, the Bad and the Ugly utilization of a channel
[[Channel utilization]]
[How do we handle Channel Busy on RF and the factors that could contribute?](https://community.arubanetworks.com/browse/articles/blogviewer?blogkey=57313b3d-f07e-4bbb-8ada-41ee62fe68ce) from Aruba
[Old but gold, AireOS DCA Dynamic Channel Assignment](https://mrncciew.com/2013/03/16/configuring-dca/)
[Wi-Fi Airtime Utilization](https://www.csbtech.net/blog/2016/3/1/airtime-fxjhg)
*This was the hardest part of the article*

Channel utilization is a layer one measurement of the percentage of time a 802.11 channel is used above a amplitude threshold, usually of -95 dBm, within a timespan (which is mostly 30 seconds in all the Channelyzer gifs in this article).

Some tech articles write that the definition of airtime and channel utilization are interchangeable **but** [Joel Crane on 2018 Wi-Fi Trek conference](https://www.youtube.com/watch?v=KAYEo_V9Gqc) split those two definitions:
- Channel utilization is a layer 1 measurement of the average percent of utilization across an 802.11 channel. It is measured with a spectrum analyzer.
- Airtime is a layer 2 measurement of time reserved on the air by 802.11 stations. It is measured with a packet analysis of 802.11 frames.

In this Channelyzer capture of a file transfer test (channel 116, 802.11ac) we can see 
- the utilization threshold set at -90 dBm
- the light blue utilization graph rising from 20 to 100%
- the whaterfall graph changing color from blue (not busy) to green and finally red (fully occupied).
- ignore my mouse overing on things :alien:
![A speedtest on Channelyzer software](chzer_download.gif)

I tested the same file transfer on the super chaotic 2.4 GHz spectrum in my home (802.11g, channel 13)
![[/img/chzer_download_2.gif]]

We can see 
- the utilization threshold set at -85 dBm trying to cover the massive background RF noise
- the light blue utilization graph rising from a baseline of 20 to 80%
- the whaterfall graph is a rainbow of red and green because of co-channel interference from other access point in other apartments
- in the fart left is visible the signal from my volumetric sensor :heavy_exclamation_mark:

The tricky thing about this percentage value is that the same amount of throughput can occupy the RF medium with different percentages because lower speed modulation pack fewer bits than higher speed modulation into a given time interval. So a 10 Mbps throughput require different channel utilization depending on the capabilities of a wireless client.

*"But I can use 40, 80 or even 160 MHz channels to speed things up, right?"* Increasing the bandwidth of a channel may improve channel utilization but you need to be careful and not fall into the trap of co-channel interference rising in unfortunately the utilization.

So when high utilization is a sign of link congestion and when is good sign of a channel being used efficiently? Ben Miller tries to give an explanation during the [WLPC of 2019](https://www.youtube.com/watch?v=A7oxqX8z_Ks) using this table:

| Good      | Bad |
| ----------- | ----------- |
| High rate frames so less channel time used for data      | Low rate frames so more channel time used for data       |
| Successful frames so the channel is used for data   | Unsuccessful frames so the channel is wasted        |

What can cause bad utilization of a channel?
- A very high number of clients under an access point regardless of the protocol and data rates used.
- Low SNR
	- Interferences over (rising collisions) and above (lowering SNR) the CCA ED threshold (Clear Channel Assessment, Energy Detection):
		- "Friendly fire" interferences like cranking up to the maximun transmit power of all the access point in a semi-close enviroment.
		- "Rogue" interferences from access point in near buildings and interferences from non-Wi-Fi signals in the same spectrum of Wi-Fi
# Title

![A speedtest on Channelyzer software](/gif/chzer_speedtest.gif)

# Title

![Downloading a big file on Channelyzer software](/gif/chzer_download.gif)

# Title

![Downloading a big file on Channelyzer software](/gif/chzer_download_2.gif)

# Title

![Downloading a big file on Channelyzer software](/gif/chzer_streaming.gif)

# Title

![Downloading a big file on Channelyzer software](/gif/chzer_video.gif)

# Title

![Downloading a big file on Channelyzer software](/gif/chzer_download.gif)