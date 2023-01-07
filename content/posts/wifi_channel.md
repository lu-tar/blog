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

![](/gif/old-tv.gif)

This article is a mix of what I've learned in the past few years and more recent studies on the RF physical layer which forced me to investigate in depth concepts like: encoding, spectral mask, path loss or the Shannon limit.

The external references I've used are in the bottom of the page.

## What is a wireless channel? 
Before addressing the individual topics, this is mine definition of a wireless channel based on what I've learned so far: 

>a channel is a numbered, shared, layer 1 domain with a specific bandwidth (often expressed in MHz) and a center frequency.

Let's unpack the little, over condensed, definition of channel:
- A RF channel is **numbered** because for sure is easy to remember channel "1" instead of a "22 MHz channel with center frequency of 2412 MHz between 2401 and 2423 MHz".
- A RF channel is a **shared** medium because, unlike IEEE 802.3 Ethernet which is protected from the outside, there is no "a shielded twisted pair" over the air and interferers and ground noise are variables to be strongly taken into consideration.
- A RF channel is a **layer 1 domain** because it sits alongside 802.3 in the bottom of the ISO-OSI pile.
- A Wi-Fi RF channel uses **bandwidth** of 20 MHz for OFDM-based transmissions and 22 MHz for DSSS-based, but we can bond channel together and double the bandwidth up to 160 MHz. 20, 40, 80 and 160 MHz are the wireless enterprise standards but some technologies can use 1 (like Bluetooth), 5 or 10 MHz bandwidth.
- The **center frequency** of a RF channel is the peak frequency where most of the information are transmitted and isolated from other channels. 2.4 GHz and 5 GHz are the main portion of the spectrum used by the enterprise wireless vendors.

This definition can surely be expanded according to your background but for a networking engineer with a mixed knowledge of routing and switching and wireless is a good starting point.

In the next section I'm going to unpack different concepts:
1. The shape of a wireless channel
2. Channel bonding
3. Good and bad channel utilization, the Shannon limit
4. Co-channel interference
5. Channel assignment algorithms: Cisco DCA

### OFDM vs DSSS and spectrum mask or "what the hell am I watching?!? 🤌"
The first day I opened Channelyzer I thought "What the hell am I watching?!" and that's the root question of this paragraph: why we see a *hill* shape or a *squared shape* 

OFDM ad DSSS are the two major encoding scheme that I've learned
That answer the question "how are bits actually carried over the air with radio signals"

> The 802.11 standard specifies a spectral mask which defines the permitted power distribution across each channel. The spectral mask requires the signal be attenuated to certain levels (from its peak amplitude) at specified frequency offsets. The spectral mask used for the 802.11b standard. While the energy falls off very quickly from its peak, it is interesting to note the RF energy that is still being radiated at other channels.
![DSSS channel](/img/spectral_dsss.png)
![2.4 GHz channels on Channelyzer](/img/channels.png)

> For the 802.11a, 802.11g, 802.11n and 802.11ac standards that are using the OFDM encoding scheme, they have a spectral mask that looks entirely different. OFDM allows for a more dense spectral efficiency, thus it gets higher data throughput than used with the BPSK/QPSK techniques in 802.11b

![OFDM channel](/img/spectral_ofdm.png)
![OFDM channel](/img/channel_square.png)

### Channel bonding or "why I need to be careful with 40 MHz on 802.11n, 80 or 160 MHz"
[# Mama Says Channel Bonding is the Devil by Nick Shoemaker](https://blogs.arubanetworks.com/solutions/mama-says-channel-bonding-is-the-devil/)

### The Good, the Bad and the Ugly utilization of a channel
The utilization of a channel is one of the variables 
Channel utilization is a layer one measurement of the percentage of time a 802.11 channel is used above a amplitude threshold, usually of -95 dBm, within a time-span (which is mostly 30 seconds in all the Channelyzer gifs in this article).

Some tech articles write that the definition of airtime and channel utilization are interchangeable **but** Joel Crane on 2018 Wi-Fi Trek conference [^1] splits those two definitions:
- Channel utilization is a layer 1 measurement of the average percent of utilization across an 802.11 channel. It is measured with a spectrum analyzer.
- Airtime is a layer 2 measurement of time reserved on the air by 802.11 stations. It is measured with a packet analysis of 802.11 frames.
Splitting the definition is ok but remember that this two variables often times are correlated with each other.

In this Channelyzer capture of a file transfer test (channel 116, 802.11ac) we can see: 
- the utilization threshold set at -90 dBm
- the light blue utilization graph rising from 20 to 100% on the bottom of the screen
- the waterfall graph changing color from blue (not busy) to green and finally red (fully occupied).
- ignore my mouse hovering on things :alien:
![File transfer capture on Channelyzer software](/gif/chzer_download.gif)

I tested the same file transfer on the super chaotic 2.4 GHz spectrum in my home (802.11n, channel 13):
![File transfer capture using 802.11g](/gif/chzer_download_2.gif)

We can see 
- the utilization threshold set at -85 dBm
- the light blue utilization graph rising from a baseline of 20 to 80%
- the waterfall graph is a rainbow of red and green because the spectrum is very crowded with other access point in other apartments + wireless repeaters.
- in the fart left is visible the signal from my volumetric sensor :heavy_exclamation_mark: and an intermittent transmission occurring on channel 1, 2 and 3 :heavy_exclamation_mark:

To understand how the other traffic flow is graphically represented, I started two captures on two different data flows: a streaming service and a buffered audio/video service like YouTube:

The capture of a streaming service data flow
![](/gif/chzer_streaming.gif)

The capture of a YouTube data flow
![](/gif/chzer_video.gif)

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
	- Interferences over (rising collisions) and above (lowering SNR) the CCA ED threshold (Clear Channel Assessment, Energy Detection)
		- "Friendly fire" interferences like cranking up to the maximum transmit power of all the access point in a semi-close environment.
		- "Rogue" interferences from access point in near buildings and interferences from non-Wi-Fi signals in the same spectrum of Wi-Fi

Here's a sentence with a footnote.       

[^1]: Joel Crane on 2018 Wi-Fi Trek [conference](https://www.youtube.com/watch?v=KAYEo_V9Gqc)
[^2]: 
[^3]:
[^4]:
[^5]:
[^6]: ASS

In relation of 802.11b [Clear channel assessment attack](https://en.wikipedia.org/wiki/Clear_channel_assessment_attack)

## References
- 802.11 Wireless Networks by Matthew Gast (Amazon [link](https://www.amazon.it/802-11-Wireless-Networks-Definitive-Guide/dp/0596100523/ref=sr_1_1?__mk_it_IT=%C3%85M%C3%85%C5%BD%C3%95%C3%91&crid=1PGLL3OA6W6QZ&keywords=Wireless+Networks+by+Matthew+Gast&qid=1673100670&sprefix=wireless+networks+by+matthew+gast%2Caps%2C151&sr=8-1))
- [Wikipedia Physical Layer: Services](https://en.wikipedia.org/wiki/Physical_layer#Services)
- [Use Channel Utilization in Wi-Fi Ben Miller WLPC Phoenix 2019](https://youtu.be/A7oxqX8z_Ks)
- [This random oscilloscopes company guide on Wi-Fi: Overview of the 802.11 Physical Layer and Transmitter Measurements](https://www.cnrood.com/en/media/solutions/Wi-Fi_Overview_of_the_802.11_Physical_Layer.pdf)
- [Aruba's Push It To The Limit! Understand Wi-Fi’s Breaking Point to Design Better WLANs](https://blogs.arubanetworks.com/industries/push-it-to-the-limit-understand-wi-fis-breaking-point-to-design-better-wlans/)
- [Jeremy Sharp's blog on Spectrum Analysis – PHYs and Interferers](https://howiwifi.com/2020/07/03/spectrum-analysis-phys-and-interferers)
- [How do we handle Channel Busy on RF and the factors that could contribute?](https://community.arubanetworks.com/browse/articles/blogviewer?blogkey=57313b3d-f07e-4bbb-8ada-41ee62fe68ce) from Aruba
- [Old but gold, AireOS DCA Dynamic Channel Assignment](https://mrncciew.com/2013/03/16/configuring-dca/)
- [Wi-Fi Airtime Utilization](https://www.csbtech.net/blog/2016/3/1/airtime-fxjhg)