---
title: index a CLI cheat sheet
date: 2023-09-07T23:38:11+02:00
draft: false
tags:
  - cmd
  - command
categories:
  - cheat sheet
cover:
  image: img/index.png
  alt: ""
  caption: ""
ShowToc: false
author:
  - Luca
---
# `index v1.0`
Welcome to *index*, we'll explore useful Command Line Interface (CLI) commands for troubleshooting with a focus on three these vendors: Cisco, Aruba, and Palo Alto.
I will try my best to keep this article up to date, incorporating the latest tips and commands.
## Cisco

### Troubleshooting Cisco 9800 SSO
Cisco 9800 SSO allows two Cisco 9800 Series Wireless LAN Controllers to work in an active-standby configuration, where one controller takes over if the other fails. This ensures uninterrupted network service for wireless clients. Run the commands below if any unexpected switchover was to happen:
```
term len 0
dir all | i cor
show proc cpu sorted | i CPU
show proc cpu platform sorted 1min
show chassis
show chassis ha-status local
show romvar
show redundancy
show redundancy switchover history
show redundancy history
sh log | i REDUNDANCY
show logging process stack_mgr internal
```
### 9800-CL VM scaling and profiling from [VM scaling guide](https://www.cisco.com/c/en/us/products/collateral/wireless/catalyst-9800-cl-wireless-controller-cloud/nb-06-cat9800-cl-wirel-cloud-dep-guide-cte-en.html)
When updating a controller, pay attention to the resources to allocate to it because they may increase from one version to another.
```
show platform software system all
```

To avoid stability and performance issues, it’s advisable **to fully reserve the vCPU** resources needed for the 9800-CL and never oversubscribe them. Hyperthreading is not supported and will need to be disabled on the host machine.

2 Starting from Cisco IOS XE Amsterdam 17.3.1, the required storage has increased from 8 GB to 16 GB. If upgrading to Cisco IOS XE Amsterdam 17.3.x from a previous release, the existing storage can be kept at 8 GB. For all new installations, it is required to go to 16 GB.
#### Cisco AP
##### Reset configuration
```
clear capwap private-config  
clear lwapp private-config  
reset
```
##### Unlock CLI commands like reload
It's an hidden command!
```
debug capwap console cli
```
##### Client channel bandwidth from access point
```
show controllers dot11Radio 1 client [MAC]
```

Refer to this [guide](https://www.cisco.com/c/en/us/td/docs/wireless/access_point/tech-notes/ap-radio-reset-codes.html) to dive into Radio Reset Codes or Radio RC stats.
#### AireOS terminal length to zero
```
config paging disable
config paging enable
```
