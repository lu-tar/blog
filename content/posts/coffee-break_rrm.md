---
title: "A coffee-break article about RRM"
date: 2023-03-18T18:10:11+02:00
draft: false
tags: ["wireless", "rrm", "dca", "power", "channel", "design", "tpc"]
categories: ["coffee-break"]
cover:
  image: img/nuclear_terminal.png
  alt: ''
  caption: ''
ShowToc: true
author: ["Luca"]
---
# A *coffee break* article about RRM

Why, when and how we use vendor's algorithms, like ARM or RRM, to manage channels and transmit power of our access points? Why sometimes when I configure the RRM I feel like I am touching the buttons of a nuclear reactor control plant?

All this nonsense started from [this r/networking article ](https://www.reddit.com/r/networking/comments/uof1l6/is_wifi_dca_working_for_you/)written 10 months ago. I advise you to go and read it and also read the comments of the users. The user (the Reddit Guy) wonders if on his university campus (500 Cisco 9130 with a pair of 9800-40) RRM algorithms are the optimal solution to channel and power management.

I'm still studying these algorithms and how to do effective troubleshooting on them so I will cover my ignorance on the matter using a lot of irony and bad jokes. Enjoy the article! :smile:

## Is there a RF engineer inside my WLC?
Let's start with some marketing fluff both from Aruba
> Aruba's Adaptive Radio Management (ARM) technology maximizes WLAN performance even in the highest traffic networks by dynamically and intelligently choosing the best 802.11 channel and transmit power for each Aruba AP in its current RF environment.

and my favourite from Cisco
> The Radio Resource Management (RRM) software that is embedded in the device acts as a built-in Radio Frequency (RF) engineer to consistently provide real-time RF management of your wireless network.

and still from Cisco
>AI-Enhanced RRM is the next evolution of Cisco’s award-winning radio resource management (RRM). RRM was originally introduced with Cisco® AireOS and the Cisco Aironet® access points in 2005 and managed the complexities of RF from Wi-Fi 1 through 6 and now Wi-Fi 6E. RRM has fluidly grown to include innovative algorithms such as Flexible Radio Architecture (FRA) and Dynamic Bandwidth Selection (DBS) to the traditional algorithms of Dynamic Channel Assignment (DCA) and Transmit Power Control (TPC).

So from the get go the IT manager who used access points like the 1242ag for 10 years is greeted with innovative, life-freeing algorithms and decides to invest tens of thousands of money in the next generation of wireless controllers to renew the Wi-Fi infrastructure of the 2000s to one of the 2080s. A pair of WLC and 50 access points are sold and installed, these specific numbers will become useful later in the article. Remember that *there is a built-in Radio Frequency (RF) engineer* inside the controller. How much do they pay him/her? Can he/she go on vacation and how did he/she win an award? Is there a competition for radio resource management? We will never know.

After the WLC, access point installation and configuration is finished, the RRM is tuned following the customer feedbacks and often times parts of the configuration are left *defaults*. All is good, test on-site are flawless, if there is time left we even do an RF site survey and then the project is finally ready for production. At this point the wireless infrastructure feels like those retro-futurism posters of the 80s imagining the future decades.
![](/img/retrofuturism.png)

Our managers are happy, customer is happy, the sales guy gets a promotion and marketing team newsletters are flying all around pumping up the hype for the project, everybody is shaking hands with each other, maybe hugging. Good times.

## Time pass
Weeks, months or maybe years pass and the customer starts complaining about dropping connections, sub-optimal roaming patterns and bad wireless performance in general like the guy in the aforementioned reddit post:
>About halfway through the deployment, I was getting complaints. Dropped connections, poor performance, etc... I went to investigate and I had APs channel overlapping each other and in some cases multiple, all on the same channel and autopowered down to the minimum. In addition, the 2.4 radios were also stepping on each other.

Now the Pandora's box is opened and after months from the installation the root cause or cause**s** of the issue can hide behind a lot of things: a not optimal design from the start, a misconfiguration, a software or hardware bug, a new alarm or video surveillance system, the type of materials on the shelves or the positioning of the racks themselves, a change on the LAN infrastructure like a new firewall or a WAN problem that impacts the user experience. The list goes on.
![](/img/pandora.png)

The troubleshooting starts and you stumble over the RRM. You try to read the documentation and configure it properly, but there is always something that is statically configured from the installation for one reason or another, whether it is the transmission power, the channels range or the bandwidth. At the end the Reddit Guy finds that the "static path" is the better solution for his campus, making it clear that the design was done with Ekahau and not by hand drawing the ap coverage on the floor plan.
>I ended up dropping the them all down to 20Mhz channels and statically setting radios after surveying the areas. Turned off 2.4 everywhere unless it's specifically requested and manually adjusted power levels for the AP's that turned themselves all the way down for no apparent reason. Still tuning in some areas.

So...

## How can we balance the dynamic and the static configuration?
These are the questions that I would ask myself during a pre-deployment phase
- **Network size**: how big is the network? How many locations? 3-4 headquarters-like buildings or 300 small buildings of 5 to 10 access points each one. 
- **Neighbors**: is the network in the middle of a shopping center with the other stores firing up their own wireless network? Or is the building in the middle of nowhere? 
- The effort put in **monitoring**: how it is done? By hand or by automation? Cisco Prime / Airwave reports?
- How skilled is the IT team on-site with RF and wireless troubleshooting? **How fast can the team react to** a rogue access point disrupting the RF spectrum? How fast can the team react to a change in the building blueprint like a new wall or a change in the warehouse space? 
- Is **5 GHz** your number one priority in terms of channel planning?

Therefore: 
- If the network is big, the environment is constantly changing and the 5GHz is the main frequency, then a dynamic approach is the better solution. 
- But, if the environment has under 100 access points (as the one that was sold at the start of the article), is well known and easily "reachable" from the staff (not like small remote size offices) then I think that a static approach is starting to make sense with a lot of monitoring and recurring tests. A deployment with 2.4 GHz, having only 3 channels, I think it always needs an ad-hoc channel planning from the start if the environment is a production site or the main headquarter.
- Or maybe I am completely wrong and the DCA and the TPC need only a very good configuration tuning supported by a strong design from the initial installation. Just that.

## Last but not the least
This is the comment under the Reddit Guy post that sparked the entire article
![](/static/img/thesesimplewords.png)
