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
ShowToc: true
author:
  - Luca
---
# `index v1.0`
Welcome to *index*, we'll explore useful Command Line Interface (CLI) commands for troubleshooting with a focus on three these vendors: Cisco, Aruba, and Palo Alto.
I will try my best to keep this article up to date, incorporating the latest tips and commands.

#### *Useful wireless-related links:*

- General troubleshooting
	- [Cheat Sheet Common Wireless Issues - Cisco](https://www.cisco.com/c/en/us/support/docs/wireless/5508-wireless-controller/200072-Cheat-Sheet-Common-Wireless-issues.html)
	- [Understanding Debug Client on Wireless LAN Controllers (WLCs) - Cisco](https://www.cisco.com/c/en/us/support/docs/wireless/aironet-1200-series/100260-wlc-debug-client.html#eap_auth)
	- [Troubleshooting user connectivity issues on Instant AP. - Airheads Community](https://community.arubanetworks.com/t5/Controllerless-Networks/Troubleshooting-user-connectivity-issues-on-Instant-AP/m-p/79610/highlight/true#M1969)
	- [802.11 Association Status, 802.11 Deaut... - Cisco Community](https://community.cisco.com/t5/wireless-mobility-documents/802-11-association-status-802-11-deauth-reason-codes/ta-p/3148055)
	- [Wireless Troubleshooting Tools - WCAE/WLCCA/Telemetry - Cisco DevNet](https://developer.cisco.com/docs/wireless-troubleshooting-tools/)
	- [Wireless Software Matrix](https://www.cisco.com/c/en/us/td/docs/wireless/compatibility/matrix/compatibility-matrix.html)
	- [Cisco AP Channel Utilization](http://wifinigel.blogspot.com/2015/01/cisco-ap-channel-utilization.html)
	- [Wireless Debug Analyzer](https://cway.cisco.com/tools/WirelessDebugAnalyzer/)
	- [Bug Search Tool](https://bst.cloudapps.cisco.com/bugsearch)
	- 
- 802.1X e EAP
	- [802.1X e EAP](https://wifininjas.net/2020/01/10/wn-blog-026-802-1x-eap/)
	- [WLC Client Debug – Part 3 | mrn-cciew](https://mrncciew.com/2019/06/03/wlc-client-debug-part-3/)
	- [Following the 802.1X AAA process with Packet Captures](https://wirelesslywired.com/2017/07/05/following-the-802-1x-aaa-process-with-packet-captures/)
	- [Wireless 802.1X Troubleshooting – How I WI-FI](https://howiwifi.com/2020/07/19/wireless-802-1x-troubleshooting/)
- Data rates
	- [MCS table](https://mcsindex.net/)
	- [Client Troubleshooting – Unsupported Data Rates | CCIE Wireless (v3) Study Notes](https://cciewirelessnotes.wordpress.com/2015/09/15/client-troubleshooting-unsupported-data-rates/)
- OTA e pcap
	- [AP sniffer mode](https://www.cisco.com/c/it_it/support/docs/wireless/catalyst-9800-series-wireless-controllers/217057-configure-access-point-in-sniffer-mode-o.html)
	- [Configure Packet Captures on AireOS WLC - Cisco](https://www.cisco.com/c/en/us/support/docs/wireless-mobility/wireless-lan-wlan/211342-packet-captures-on-aireos-wlc.html)
	- [Wireshark coloring rules for wifi](https://support.metageek.com/hc/en-us/articles/204425074-Eye-P-A-Coloring-Rules-for-Wireshark)
- AAA
	- [AAA Return or Response Codes - Cisco Community](https://community.cisco.com/t5/wireless-mobility-documents/aaa-return-or-response-codes/ta-p/3149403)
- Cellular uplink
	- [Support for 4G USB Modems](https://www.arubanetworks.com/techdocs/ArubaOS_87_Web_Help/Content/arubaos-solutions/rap/prov-4g-usb-rap.htm) - Aruba
	- [3G/4G Uplink](https://www.arubanetworks.com/techdocs/Instant_40_Mobile/Advanced/Content/UG_files/Uplink_conf/ConfiguringCellularUplinks.htm) - Aruba
- Logs
	- [Cisco Wireless LAN Controller Software - Error and System Messages - Cisco](https://www.cisco.com/c/en/us/support/wireless/wireless-lan-controller-software/products-system-message-guides-list.html)
- VOIP
	- [How to get your 8821/792x wireless phones performing reliably - Cisco](https://www.cisco.com/c/en/us/support/docs/collaboration-endpoints/wireless-ip-phone-8821/214215-how-to-get-your-8821-792x-wireless-phone.html)
- Talks, webinars
	- [Wireless LAN Professionals](https://wlanprofessionals.com/)

---

## Cisco

### Cisco switch 
#### Stack
You can login to individual switches that belong to one stack by using the session command
```text
switch-1#session 2
switch-1-2#
```
Where as session 2 is the second switch, so session 3 for the third switch etc.
#### Error disable timer

Default 600 s

```text
IT-MZI-SSS-3FU-D4-SW047#sh errdisable recovery 
ErrDisable Reason            Timer Status
-----------------            --------------
arp-inspection               Enabled
bpduguard                    Enabled
channel-misconfig (STP)      Enabled
dhcp-rate-limit              Enabled
[...]
storm-control                Enabled
udld                         Enabled
vmps                         Enabled
psp                          Disabled
dual-active-recovery         Disabled
evc-lite input mapping fa    Disabled
Recovery command: "clear     Disabled

---> Timer interval: 600 seconds <---

Interfaces that will be enabled at the next timeout:
```


---
### Cisco 9800
#### Troubleshooting SSO
Cisco 9800 SSO allows two Cisco 9800 Series Wireless LAN Controllers to work in an active-standby configuration, where one controller takes over if the other fails. This ensures uninterrupted network service for wireless clients. Run the commands below if any unexpected switchover was to happen:
```text
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
#### 9800-CL VM upgrading and profiling
When updating a controller, pay attention to the resources to allocate to it because they may increase from one version to another.
```text
show platform software system all
```

To avoid stability and performance issues, it’s advisable **to fully reserve the vCPU** resources needed for the 9800-CL and never oversubscribe them. Hyperthreading is not supported and will need to be disabled on the host machine.

2 Starting from Cisco IOS XE Amsterdam 17.3.1, the required storage has increased from 8 GB to 16 GB. If upgrading to Cisco IOS XE Amsterdam 17.3.x from a previous release, the existing storage can be kept at 8 GB. For all new installations, it is required to go to 16 GB.

For 17.4 and later releases, this step is mandatory for the upgrade to be persisitent. If you do not click Commit, the auto-timer terminates the upgrade operation after 6 hours, and the controller reverts back to the previous image.

```text
show version | in Installation mode is
```

Per controllare lo stato dell'installazione:
```text
show install summary
```
Output:
```text
mon-a2-wlc-01#show install summary 
[ Chassis 1/R0 2/R0 ] Installed Package(s) Information:
State (St): I - Inactive, U - Activated & Uncommitted,
            C - Activated & Committed, D - Deactivated & Uncommitted
--------------------------------------------------------------------------------
Type  St   Filename/Version
--------------------------------------------------------------------------------
IMG   C    17.09.03.0.4111

--------------------------------------------------------------------------------
Auto abort timer: inactive
--------------------------------------------------------------------------------
```

#### Efficient image upgrade - Predownload
Configuration > Tags > Flex > General > Efficient Image Upgrade

Configuration > Wireless > All Access Points > Select Action (top right) > AP Image Management > Predownload Image

Efficient Image upgrade is an efficient way of predownloading the image to the APs. It works similar to primary - subordinate model. An AP per model becomes the primary AP and downloads image from the controller through the WAN link. Once the primary AP has the downloaded image, the subordinate APs starts downloading the image from the primary AP. In this way, WAN latency is reduced. Primary AP selection is dynamic and random. A maximum of three subordinate APs per AP model can download the image from the primary AP.

The following output displays the primary AP.
```
show ap master list
```

The following output shows that the primary AP has started predownloading the image.
```
show ap image
```

To check if Flexefficient image upgrade is enabled in the AP, use the `show capwap client rcb` command on the AP console.

---
### Cisco AP
##### Reset configuration
```text
clear capwap private-config  
clear lwapp private-config  
reset
```
##### Unlock CLI commands like reload
It's an hidden command!
```text
debug capwap console cli
```
##### Client channel bandwidth from access point
```text
show controllers dot11Radio 1 client [MAC]
```

Refer to this [guide](https://www.cisco.com/c/en/us/td/docs/wireless/access_point/tech-notes/ap-radio-reset-codes.html) to dive into Radio Reset Codes or Radio RC stats.
##### AireOS terminal length to zero
```text
config paging disable
config paging enable
```
##### TAC AP (IOS-XE) side debugging
```text
AP# config ap client-trace address add <client_MAC-address>
AP# config ap client-trace filter all enable
AP# config ap client-trace output console-log enable
AP# config ap client-trace start
AP# term mon
```

---
### AireOS
#### RRM
Use these commands to troubleshoot and verify RRM behavior:
`debug airewave-director ?`
- **all**—Enables debugging for all RRM logs.
- **channel**—Enables debugging for the RRM channel assignment protocol.
- **detail**—Enables debugging for RRM detail logs.
- **error**—Enables debugging for RRM error logs.
- **group**—Enables debugging for the RRM grouping protocol.
- **manager**—Enables debugging for the RRM manager.
- **message**—Enables debugging for RRM messages.
- **packet**—Enables debugging for RRM packets.
- **power**—Enables debugging for the RRM power assignment protocol as well as coverage hole detection.
- **profile**—Enables debugging for RRM profile events.
- **radar**—Enables debugging for the RRM radar detection/avoidance protocol.
- **rf-change**—Enables debugging for RRM RF changes.
#### Term length to zero
```text
config paging disable
```

```text
config paging enable
```

#### Password recovery
The full list of commands needed to recover the RADIUS secrets on a Cisco 5508 WLC are:

1. SSH into the WLC
2. Enter the command:
```
config password-cleartext enable
```
4. . You will be prompted to re-enter your password
	1. Save the configuration with:
**save config**
6. Issue the Show Technical Support command:
**show tech-support**

#### Exclusion list

See a list of clients that have been dynamically excluded, by entering this command:  
```text
show exclusionlist
```

Tab Security > Wireless Protection Policies > Client Exclusion Policies

- **Excessive 802.11 Association Failures**—Clients are excluded on the sixth 802.11 association attempt, after five consecutive failures.  
• **Excessive 802.11 Authentication Failures**—Clients are excluded on the sixth 802.11 authentication attempt, after five consecutive failures.  
• **Excessive 802.1X Authentication Failures**—Clients are excluded on the fourth 802.1X authentication attempt, after three consecutive failures.  
• **IP Theft or IP Reuse**—Clients are excluded if the IP address is already assigned to another device.  
• **Excessive Web Authentication Failures**—Clients are excluded on the fourth web authentication attempt, after three consecutive failures.

---
## Aruba
### Instant AP
- IAP `show ap debug client-stats client-mac <macofclient>`
- Aruba WPA PSK error:
`Aruba13# show log security | include d4:e6`
``Feb  7 16:39:02  stm[2657]: <132094> <WARN> |AP Aruba13@192.168.60.22 stm|  MIC failed in WPA2 Key Message 2 from Station d4:e6:b7:e3:46:c3 a8:bd:27:d9:42:e0 Aruba13

Debug client:
- `show ap debug client-stats client-mac <macofclient>`
- `show ap debug client-table ap-name <name of ap>`

BSSID table: `show ap bss-table`
- `show cluster bss-table | include [SSID NAME]`
```text
show cluster bss-table | include MAGAZZINO
```
```
24:f2:7f:1f:67:17  0      WIFI_MAGAZZINO      AP02_Vittuone  172.16.21.82
24:f2:7f:15:6b:97  0      WIFI_MAGAZZINO      AP03_Vittuone  172.16.21.83
24:f2:7f:1f:87:f7  0      WIFI_MAGAZZINO      AP01_Vittuone  172.16.21.81
24:f2:7f:14:7a:d7  0      WIFI_MAGAZZINO      AP08_Vittuone  172.16.21.88
[...]
```

LIST CLIENTS
- `show clients`

LIST ACCESS POINTS 
- `show aps | i AP-28`
#### Channel occupation with show ap debug radio-stats 0 or 1
```text
AP_6_A# show ap debug radio-stats 1 | include Busy
Channel Busy 1s                     29
Channel Busy 4s                     30
Channel Busy 64s                    31
Ch Busy perct @ beacon intvl        29 29 29 29 29 29 29 29 29 29 29 31 31 31 31 31 31 31 31 31 31 28 28 28 28 28 28 28 28 28

AP_6_A# show ap debug radio-stats 1 | include Noise
Current Noise Floor                 96
```

#### Process interaction with `sigkill` and `restart`
`show cpu`

`process restart stm sigkill`
`process restart stm`

### Mobility controller
#### Access-list and firewall
```text
show ap database
show ap database | include Padova
show user | include 172.20.48.49
```

```text
// List connection from and to AP's clients
show datapath session ap-name RAP_d0d3e0cbb43e_Padova table | include 172.20.48.49
show datapath session ap-name RAP_d0d3e0cbb43e_Padova table | include 172.20.48.49 | include 3389
```

```text
// List the ACLs for a role
show rights [role]
```

```text
// Look at counters incrementing
show acl hits 
```

```text
show datapath session ap-name RAP_d0d3e0cbb43e_Padova table | include 172.20.48.49 | include 3389
show acl hits
```

#### Aruba SNMP walking
There is no snmp agent on the APs, for what you want, query the wlsxWlanAPTable (aruba-wlan.my) on the controller, it has name, uptime, model, serial etc. e.g. check the available fields using this or in the mib
`snmpwalk -v2c -c public123 -O0X -mALL -M/home/mibs/6.5.4.3 192.168.1.123  wlsxWlanAPTable`
or
`snmptable -v2c -c public123 -O0X -mALL -M/home/mibs/6.5.4.3 192.168.1.123  wlsxWlanAPTable`

---
## HP Procurve
### Spanning tree
Topology change da output `show spanning-tree`
```text
  STP Enabled   : Yes
  Force Version : MSTP-operation
  IST Mapped VLANs : 1-4094
  Switch MAC Address : 308d99-3ec800
  Switch Priority    : 4096
[...]
  Topology Change Count  : 2921
  Time Since Last Change : 86 mins <---
  ```

#### STP counters
`show spanning-tree debug-counters ports all instance 0`
**Topology changes Tx**
Times that Topology Change information is propagated (sent out) through the port to the rest of the network.
For a CIST port, the counter is the number of times that a CFG, RST or MST BPDU with the TC flag set is transmitted out of the port.
For an MSTI port, the counter is the number of times that a MSTI configuration message with the TC flag set is transmitted out of the port.
This counter is maintained on a per-CIST per-port and on a per-MSTI per-port bases.

**Topology changes Rx**
Times that Topology Change information is received from the peer port.
For a CIST port, the counter is the number of times that a CFG, RST or MST BPDU with the TC flag set is received.
For an MSTI port, the counter is the number of times that an MSTI configuration message with the TC flag set is received.
This counter is maintained on a per-CIST per-port and on a per-MSTI per-port basis

---
## WinŽøŽ
### Grep exclude
```bash
findstr /v ASSOC 301181-AP01.txt > AP01.txt
```
### Netsh wireless stats
| Command | Description |
| ----------- | ----------- |
| `netsh wlan show drivers` | Version, compatibility, management frame protection |
| `netsh wlan show interfaces` | Interface name, 802.11 protocol, channel, data rates, BSSID |
| `netsh wlan show networks` | List ssid |
| `netsh wlan show networks mode=bssid` | Detailed list: signal, data rates, radio type |

### Recursive ping with timestamp
Da Powershell: 
```bash
ping.exe -t 8.8.8.8|Foreach{"{0} - {1}" -f (Get-Date),$_}
```
