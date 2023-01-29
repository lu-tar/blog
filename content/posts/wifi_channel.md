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

## A big premise
This article is a mix of what I've learned in the past few years and more recent studies on the RF physical layer which forced me to investigate in depth concepts like: encoding, spectral mask, Clear Channel Assessment or the Shannon limit. Stuff that you don't have to know perfectly if you're trying to achieve a mixed-work career oriented more towards R&S than wireless technologies and I probably won't study them deeply myself but I'm very curious and I find interesting knowing how things work from the bottom up. So that's it :smile:

The external references I've used are in the bottom of the page. Links to the single paragraphs are in the table of contents. Finally this [article](https://howiwifi.com/2020/07/03/spectrum-analysis-phys-and-interferers/) by Amazon wireless engineer Jeremy Sharp and the agnostic book *"802.11 Wireless Networks"* by Matthew S. Gast were both a huge inspiration while writing all this crap down. Go read it both!

## What is a wireless channel?
Before addressing the individual topics, this is the definition of a wireless channel based on what I've learned so far: 

>a channel is a numbered, shared, layer 1 domain with a specific bandwidth (often expressed in MHz) and a center frequency.

Let's unpack the little, over condensed, definition of channel:
- A RF channel is **numbered** because for sure is easy to remember channel "1" instead of a "22 MHz channel with center frequency of 2412 MHz between 2401 and 2423 MHz".
- A RF channel is a **shared** medium because, unlike IEEE 802.3 Ethernet which is protected from the outside, there is no "a shielded twisted pair" over the air and interferers and ground noise are variables to be strongly taken into consideration.
- A RF channel is a **layer 1 domain** because it sits alongside 802.3 in the bottom of the ISO-OSI pile.
- A Wi-Fi RF channel uses **bandwidth** of 20 MHz for OFDM-based transmissions and 22 MHz for DSSS-based, but we can bond channel together and double the bandwidth up to 160 MHz. 20, 40, 80 and 160 MHz are the wireless enterprise standards but some technologies can use 1 (like Bluetooth), 5 or 10 MHz bandwidth.
- The **center frequency** of a RF channel is the peak frequency where most of the information are transmitted.

This definition can surely be expanded according to your background but for a networking engineer with a mixed knowledge of routing and switching and wireless is a good starting point because it encapsulates all the core variables.

---

In the next section I'm going to unpack different concepts:
1. The shape of a wireless channel
2. Channel bonding
3. Good and bad channel utilization, the Shannon limit
4. Co-channel interference
5. Channel assignment algorithms: Cisco DCA

### Spectral mask or "what the hell am I watching?!?"
The first day I opened Channelyzer I thought "What the hell am I watching?!" and that's the root question of this paragraph: why sometimes we see a *hill* shape, a *squared* or *panettone* shape or neither of them, during a RF analysis.

![](/img/80211spectversus.png)
![](/img/80211acspectavg.PNG)
![](/img/80211sensorspect.PNG)

The *thing that I'm seeing on the screen* during a RF analysis is the live representation of a spectral mask which is a set of rules that confine a transmission in a dedicated slice of the spectrum preventing leakage of signal on the adjacent channels.

Wikipedia helps us with this definition [^1]:
> The spectral mask is generally intended to reduce adjacent-channel interference by limiting excessive radiation at frequencies beyond the necessary bandwidth. Attenuation of these spurious emissions is usually done with a band-pass filter, tuned to allow through the correct center frequency of the carrier wave, as well as all necessary sidebands.

Bits are carried over the air with radio signals using digital encoding schemes which are DSSS and OFDM. Both have different spectral masks:

DSSS
> The spectral mask used for the 802.11b standard. While the energy falls off very quickly from its peak, it is interesting to note the RF energy that is still being radiated at other channels.

![OFDM channel](/img/spectral_dsss.png)

OFDM
> For the 802.11a, 802.11g, 802.11n and 802.11ac standards that are using the OFDM encoding scheme, they have a spectral mask that looks entirely different. OFDM allows for a more dense spectral efficiency, thus it gets higher data throughput than used with the BPSK/QPSK techniques in 802.11b

![OFDM channel](/img/spectral_ofdm.png)

Everything that is not following this standards is in many cases a interference in the wifi spectrum. The fourth screenshot is the perfect example which is a volumetric sensor in my house that fortunately is working away from traditional channels.

==ADD FOOTNOTE==

### The Good, the Bad and the Ugly utilization of a channel
Channel utilization is a layer one measurement of the percentage of time a 802.11 channel is used above a amplitude threshold, usually of -95 dBm, within a time-span (which is mostly 30 seconds in all the gifs in this article).

Some tech articles write that the definition of airtime and channel utilization are interchangeable **but** Joel Crane on 2018 Wi-Fi Trek conference ==ADD FOOTNOTE== splits those two definitions:
- Channel utilization is a layer 1 measurement of the average percent of utilization across an 802.11 channel. It is measured with a spectrum analyzer.
- Airtime is a layer 2 measurement of time reserved on the air by 802.11 stations. It is measured with a packet analysis of 802.11 frames.

Splitting the definition is ok but at the end this two variables often times are very correlated with each other as Joel sad.

The tricky thing about this percentage value is that the same amount of throughput can occupy the RF medium with different percentages because lower speed modulation pack fewer bits than higher speed modulation into a given time interval. So a 10 Mbps throughput require different channel utilization depending on the capabilities of a wireless client.
==ADD FOOTNOTE==
*"But I can use 40, 80 or even 160 MHz channels to speed things up, right?"* Increasing the bandwidth of a channel may improve channel utilization but you need to be careful and not fall into the trap of co-channel interference rising in fact the utilization.

So when high utilization is a bad sign of **link** **congestion** and when is good sign of a channel being used **efficiently**? Ben Miller tries to give an explanation during the [WLPC of 2019](https://www.youtube.com/watch?v=A7oxqX8z_Ks) using this table: ==ADD FOOTNOTE==

| The Good 🟢      | The Bad 🔴 |
| ----------- | ----------- |
| High rate frames so less channel time used for data      | Low rate frames so more channel time used for data       |
| Successful frames so the channel is used for data   | Unsuccessful frames so the channel is wasted        |

Wrapping up, what can cause bad utilization of a channel?
- A **very high number of clients**, regardless of the protocol used, will always downgrade their datarates with the increasing number of stations under an access point. This effect is 10x more impacful with older protocols in the 2.4 GHz spectrum.
- Low SNR caused by
	- Interferences both over and above the CCA threshold (Clear Channel Assessment for energy detection, signal detection and network allocation vector). The first will generate a rising number collisions, the second will in fact raise the noise floor. ==ADD FOOTNOTE==
		- Wi-Fi *"friendly fire" interferences* caused by a overuse of high transmit power, bad channel plan or bad positioning of access point.
		- Wi-Fi *rogue interferences* like access point in near buildings of your campus.
		- *non-Wi-Fi* interferences trasmitting in the same spectrum of Wi-Fi.

---
In the previous paragraph we saw how a spectral mask changed shape according to the encoding protocol. So to better visualize and understand both channel utilization and the spectral mask I made 4 RF captures using [Metageek's Channelyzer](https://www.metageek.com/) of:
1. A file transfer using 802.11ac and channel 116
2. A file transfer using 802.11n and channel 13
3. A streaming service data flow using 802.11ac and channel 116
4. A buffered audio/video streaming service data flow using 802.11ac and channel 116

In the first capture of the file transfer test we can see: 
- the utilization threshold set at -90 dBm
- the light blue utilization graph rising from 20 to 100% on the bottom of the screen
- the waterfall graph changing color from blue (not busy) to green and finally red (fully occupied).
- ignore my mouse hovering on things :alien:
![File transfer capture on Channelyzer software](/gif/chzer_download.gif)

I tested the same file transfer on The Ugly :skull: super chaotic 2.4 GHz spectrum in my home (802.11n, channel 13):
![File transfer capture using 802.11g](/gif/chzer_download_2.gif)

We can see 
- the utilization threshold set at -85 dBm
- the light blue utilization graph rising from a baseline of 20 to 80%
- the waterfall graph is a rainbow of red and green because the spectrum is very crowded with other access point in other apartments + wireless repeaters
- in the far left is visible the signal from my volumetric sensor :heavy_exclamation_mark: and an intermittent transmission occurring on channel 1, 2 and 3 :heavy_exclamation_mark:

To understand better how others types of data flows is graphically represented, I started two captures on a streaming service and a buffered audio/video service like YouTube:

The capture of a streaming service data flow
![](/gif/chzer_streaming.gif)

The capture of a YouTube data flow
![](/gif/chzer_video.gif)

### Why I need to be careful with 40 MHz, 80 or 160 MHz

The same question can be formulate as: *during the troubleshooting phase or the design phase of a wireless network, do we need to take into consideration the channel overlap (both co-channel interference and adjacent channel interference)?* If high-demand application are in use, the answer is 90% of the time **yes** because, the wireless protocol being "polite" and "listen before you talk" type of protocol, as soon there is channel overlap, data corruption and collisions start to occur, clients downgrade their data rates which leads to a drop of performance and packet loss.

>Moving more data with every transmission is not better if I have to wait 3 times as long to send a single packet - the result could be worse than sending what I have more frequently, in smaller bits. Not all applications actually benefit from bonded channels; Voice for instance relies on small packets that are time sensitive (jitter). Video however benefits greatly - but still has a sensitivity to Jitter in some cases (real time video)
>==ADD FOOTNOTE== [5500 Series Wireless Controllers Radio Resource Management White Paper](https://www.cisco.com/c/en/us/td/docs/wireless/controller/technotes/8-3/b_RRM_White_Paper/dca.html#id_15210)

And the remaining 10%? The impact of channel overlap is not a 0 or 1 thing because co-channel interference is strictly inevitable and problems can be highly correlated with the application resilience or the type of client, more precisely, its roaming algorithm or Keith Parsons's [green diamond](https://wlanprofessionals.com/greendiamond/); for example we can have different performance with the same application on a client which roams using RSSI only versus more advanced roaming algorithms that include SNR in the . Therefore an application can work fine also with overlapping channels within certain limits.

Now, knowing that co-channel and adjacent channel interference are an important variable, we can going back to the title paragraph and reflect on how wide channels can be disruptive in a wireless network starting from these images
![](/img/5channels.png)
![](/img/channel_bond.png)
![](/img/channel_architecture.png)
So if we double the channel bandwidth, we double the data throughput but we reduce drastically the number of channels available risking a "friendly fire" interference which is proportionally dangerous to the number of the access point in the same area.

### Channel assignment algorithms: Cisco DCA
In this 3-4 years I worked both with Cisco AireOS and IOS-XE controllers, so I choose to study and review Cisco's proprietary DCA algorithm which is only a piece of a bigger cake called RRM or Radio Resource Management that includes:
-   Radio resource monitoring
-   Power control transmission
-   **Dynamic channel assignment**
-   Coverage hole detection and correction
-   RF grouping

All the information under this paragraph are from the [Cisco 9800 configuration guide, release 17.6](https://www.cisco.com/c/en/us/td/docs/wireless/controller/9800/17-6/config-guide/b_wl_17_6_cg/m_rrm_c9800.html) and [2021 Radio Resource Management white paper](https://www.cisco.com/c/en/us/td/docs/wireless/controller/technotes/8-3/b_RRM_White_Paper/dca.html).
The main goal of the DCA algorithms is to automate the bandwidth and channel assignment using real-time RF characteristics:
1. Access point received energy
2. Noise
3. 802.11 interference
4. Load and utilization

Access point received energy: The received signal strength measured between each access point and its nearby neighboring access points. Channels are optimized for the highest network capacity.
    
-   Noise: Noise can limit signal quality at the client and access point. An increase in noise reduces the effective cell size and degrades user experience. By optimizing channels to avoid noise sources, the device can optimize coverage while maintaining system capacity. If a channel is unusable due to excessive noise, that channel can be avoided.
    
-   802.11 interference: Interference is any 802.11 traffic that is not a part of your wireless LAN, including rogue access points and neighboring wireless networks. Lightweight access points constantly scan all the channels looking for sources of interference. If the amount of 802.11 interference exceeds a predefined configurable threshold (the default is 10 percent), the access point sends an alert to the device. Using the RRM algorithms, the device may then dynamically rearrange channel assignments to increase system performance in the presence of the interference. Such an adjustment could result in adjacent lightweight access points being on the same channel, but this setup is preferable to having the access points remain on a channel that is unusable due to an interfering foreign access point.
    
    In addition, if other wireless networks are present, the device shifts the usage of channels to complement the other networks. For example, if one network is on channel 6, an adjacent wireless LAN is assigned to channel 1 or 11. This arrangement increases the capacity of the network by limiting the sharing of frequencies. If a channel has virtually no capacity remaining, the device may choose to avoid this channel. In huge deployments in which all nonoverlapping channels are occupied, the device does its best, but you must consider RF density when setting expectations.
    
-   Load and utilization: When utilization monitoring is enabled, capacity calculations can consider that some access points are deployed in ways that carry more traffic than other access points, for example, a lobby versus an engineering area. The device can then assign channels to improve the access point that has performed the worst. The load is taken into account when changing the channel structure to minimize the impact on the clients that are currently in the wireless LAN. This metric keeps track of every access point’s transmitted and received packet counts to determine how busy the access points are. New clients avoid an overloaded access point and associate to a new access point. This _Load and utilization_ parameter is disabled by default.

The RRM startup mode is invoked in the following conditions:

-   In a single-device environment, the RRM startup mode is invoked after the device is upgraded and rebooted.
-   In a multiple-device environment, the RRM startup mode is invoked after an RF Group leader is elected.
-   You can trigger the RRM startup mode from the CLI.

DCA algorithm interval is set to 1 hour, but DCA algorithm always runs in default interval of 10 min, channel allocation occurs at 10-min intervals for the first 10 cycles, and channel changes occur as per the DCA algorithm every 10 min. After that the DCA algorithm goes back to the configured time interval. This is common for both DCA interval and anchor time because it follows the steady state.

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
- Article by Nick Shoemaker from Aruba [Mama Says Channel Bonding is the Devil by Nick Shoemaker](https://blogs.arubanetworks.com/solutions/mama-says-channel-bonding-is-the-devil/)
- [Ekahau - Demystifying Wi-Fi: Channel Planning Made Simple](https://youtu.be/TGqtsRaoj_w?t=1088)
- 

## Da aggiungere
- In relation of 802.11b [Clear channel assessment attack](https://en.wikipedia.org/wiki/Clear_channel_assessment_attack)


[^1]: Wikipedia link to spectral mask dasdasdasda