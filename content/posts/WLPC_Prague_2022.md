---
title: "WLPC Prague 2022"
date: 2022-10-28T11:22:19+02:00
draft: true
tags: ["wlpc", "wireless", "wireshark","congress", "talk", "automation", "troubleshooting", "design", "roaming"]
categories: ["notes"]
cover:
  image: img/dancing_house.jpg
  alt: ''
  caption: ''
ShowToc: true
author: ["Luca"]
---
# WLPC Prague 2022
## Notes from the event videos
These are notes from lectures on the recent Wireless Lan Professionals congress which was held in Prague in October 2022.

### [Automated Root Cause Analysis in Wireless Networks | Karan Gupta](https://www.youtube.com/watch?v=34m0u23_izY)
Why do we need automation during RCA in wireless networks?

Issue can be categorized in:
1. Connectivity issue. We can monitor tha AP state or the client state looking at debugs or logs
2. Performance or application issues are more subtle.

**RCA Engine**
Sympton is sent to the engine for example low data rates
Observation - list of clients
Domain Knowledge - How are different Wi-Fi parameters related to each other
System Knowledge - Configuration settings

Output: root causes, reccomedations and evidence

### [Designing Wi-Fi in the Worst RF in the World | Jim Palmer](https://www.youtube.com/watch?v=03JrHGQGTG4)

### [10 Wireshark Tips in 10 Minutes | Andrew McHale](https://www.youtube.com/watch?v=SX20l-ZsiEU)

### [Why Did the Zebra Cross the Road? | Stephen Cooper](https://www.youtube.com/watch?v=j3A0-EIQueE)

### [The Big Scary World of Network Integration and Automation | Peter Mackenzie](https://www.youtube.com/watch?v=QG74IZSor_s)
[Meraki API](https://developer.cisco.com/meraki/api-v1/)
Fear of network automation
From a protocol point of view an API call is based on GET and PUT request, HTTP methods that govern normal day-to-day web browsing.
- RESTful (HTTP requests)
  - Data Driven
  - Stateless
  - Resource-based
CRUD Methods: GET, POST, PUT and DELETE
We need an authorization token. The information are formatted in JSON (key valued formatting text) like 

```json
{
    "number": 1,
    "name": "My SSID",
    "enabled": true,
    "defaultVlanId": 1,
    "authMode": "8021x-radius",
    "radiusServers": [
        {
            "host": "0.0.0.0",
            "port": 1000
        }
    ],
    "encryptionMode": "wpa",
    "wpaEncryptionMode": "WPA2 only",
    "visible": true
}
```

API Endpoint URL like https://api.meraki.com/api/v1/networks/{networkId}/appliance/ssids/{number}

- WebSocket (client to server bidirectional communication)
  - Event Driven: when a client connects the system send you data
  - Stateful
 
- Webhook (server to server or reverse-API or callback-API or push-API where is the server initiating the communication)
  - Event-Driven
  - You need to have a public facing web server
