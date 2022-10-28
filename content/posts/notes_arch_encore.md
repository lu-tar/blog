---
title: "ENCORE 350-401 | Architecture"
date: 2022-10-27T16:59:10+02:00
draft: false
tags: ["encore", "350-401", "certification","encore", "cisco"]
categories: ["notes"]
cover:
image: img/bridge.jpg
alt: ''
caption: ''
ShowToc: true
author: ["Luca"]
---
# ENCORE 350-401 | Architecture
These are my notes, both in :it: ad :en:, on various topics regarding the Cisco Encore 350-401 certification "Architecture" [section](https://learningnetwork.cisco.com/s/encor-exam-topics).

These notes are made of lists and very schematic paragraphs that summarise a lot (too much) the topics that are explained in the book.

## FHRP and SSO
Le tecniche di redundancy trattate sono SSO e VSS, i First Hop Redundancy Protocols trattati sono HSRP, VRRP e GLBP.
-   SSO (≠ primary/secondary High Availability): feature che permette ad un router con due RP (Route processor) di, con un unico control plane, gestire il mirroring delle informazioni contenute nei RP (azione di checkpointing). Lo switchover dei RP triggera le adiacenze dei protocolli di routing e un clear delle route table e della CEF. Con il protocollo NFP (Non-Stop Forwarding) la CEF non si pulisce immediatamente ma per un breve periodo di tempo continua a forwardare traffico.
-   VSS (e StackWise): protocolli per il clustering di 2 switch anche separati geograficamente. The LMP or Link management protocol in stackWise implementations checks for any unidirectional link traffic forwarding.

HSRPv1
-   Multicast address: 224.0.0.2
-   UDP port 1985
-   Virtual mac: 00:00:0c:07:ac:XX
-   The HSRP group number can be from 0 to 255

HSRPv2
-   Multicast address: 224.0.0.102
-   IPV6: ff02::66
-   UDP port 1985
-   Virtual mac: 00:00:0c:9f:fX:XX
-   HSRPv2 expands the group number range from 0 to 4095

VRRPv2
-   Multicast address: 224.0.0.18
-   Virtual mac: 0000.5e00.01xx

VRRPv3
- Multicast address: 224.0.0.18
- IPV6: ff02::12
- Virtual mac: 0000.5e00.01xx

HSRP ha sei 6 stati:
1.  Initial
2.  Learn
3.  Listen
4.  Speak
5.  Standby
6.  Active

Sia HSRP sia VRRP implementano l’autenticazione dei peers confrontando una stringa oscurabile con MD5.
NB: VRRP abilita la preemption di default.
[Il TTL del pacchetto VRRP deve essere 255](https://datatracker.ietf.org/doc/html/rfc3768#section-5.2.3), come descritto nel RFC 3768.

## SD-WAN
Esistono due prodotti Cisco che fanno SD-WAN: Meraki e Cisco Viptela.

I vantaggi comprendono: rete WAN indipendente dall’underlay (4G, Internet, MPLS), gestione dei path verso applicazione SaaS, costi sulle connettività ridotti.

Esistono 4 componenti principali dell’SDWAN:

1.  vManage Network Management System (NMS): distributes policies that govern data forwarding. 
2.  [vSmart controller](https://i.imgur.com/rAuFjFf.png): router advertisement through OMP or Overlay Management Protocol. Mantiene il controllo del traffic flow di tutto il fabric.
3.  SD-WAN routers:
4.  vBond orchestrator: è un router con delle funzionalità specifiche: 
	1.  Control plane connection
	2.  NAT traversal
	3.  Load Balancing
	4.  onboarding dei router SD-WAN
	5.  STUN server

Le connessioni tra il control plane e gli SD-WAN endpoint è criptato in DTLS, Datagram Transport Layer Security is a communications protocol that provides security for datagram-based applications. The DTLS protocol is based on the stream-oriented TLS Transport Layer Security protocol and is intended to provide similar security guarantees. HTTP is encrypted with TLS.

OMP: Overlay Management Protocol, protocollo Cisco proprietario simile a BGP, si occupa di stabilire delle neighborship tra il vSmart e i router SD-WAN. OMP can advertise routes, next hops, keys, and policy information needed to establish and maintain the SD-WAN fabric (ref. pagina 634).

## SD-Access
SD-Access è la fusione tra campus fabric e Cisco DNA Center. La campus fabric è un “edge-to-edge fully automated overlay” e il DNA center è il management centralizzato. Può essere spezzato in 4 layers e 5 ruoli diversi.

**4 LAYERS:**
1.  Management layer: nel DNA center posso avere un controllo capillare di tutta l’infrastruttura.
2.  [Controller layer](https://i.imgur.com/8ePDwd5.png): è l’unione tra Cisco DNA, Cisco ISE attraverso le API ed è suddivisibile in 3 sottosistemi:
	- NCP: NCP configures and manages Cisco network devices.
	- NDP: data collection and analytics.
	- ISE: identity services for dynamic endpoint-to-group mapping and policy definition.
3.  Network layer: 
	- Underlay: trasportare i pacchetti dati con IS-IS (default, o OSPF) per overlay di tipo “layer 3 routed access” tipo le VXLAN.
		- Manuale: CLI box-to-box per fare modifiche specifiche o integrare device legacy.  
		- Automatic: l’underlay è controllato dal DNA Center LAN automation.
	-  [Overlay](https://i.imgur.com/pqpxtEf.png): garantisce network segmentation, host mobility wireless/wired e una sicurezza maggiore. Può essere configurato manualmente ma non si parlerebbe più di SD-Access ma di solo campus fabric.
		- Control plane: LISP (trasparenza dell’endpoint su tutta la rete, MS o map server).
		- Data plane: VXLAN (layer 3 encapsulation di traffico layer 2).
		- Policy plane: CTS o Cisco TrustSec permette di creare policy basate sui tag SGT e non sugli indirizzamenti di rete.
4.  Physical layer: hardware

**5 LAYERS**:
1.  Control plane node: LISP map server/ map resolver (MS/MR) - è il nodo che permette la mobilità dei client all’interno del campus fabric. Si occupa di mappare gli endpoint alle loro location ovvero gli EIDs alle RLOCs. 
2.  Fabric border nodes: LISP proxy tunnel routers (PxTRs). Connette l’esterno con l’interno e l’interno con altri domini, ci sono 3 tipi di fabric border.
	- Internal border
	- Default border
	- Internal border + default border
3.  Fabric edge nodes: fornisce un L3 unicast gateway univoco in ogni nodo fabric edge. First identifies and authenticates wired endpoints (through 802.1x) → in order to place them in a host pool (SVI and VRF instance) → and scalable group (SGT assignment) → then registers the specific EID host address (that is, MAC, /32 IPv4, or /128 IPv6) with the control plane node. In ordine:
	- Authentication
	- Host pool
	- Assegnamento del tag SGT
	- Registrazione del EID al control plane
	Il fabric node si occupa anche di incapsulare e decapsulare il pacchetti VXLAN. 
4.  [Fabric WLAN controller](https://i.imgur.com/1oMRvr4.png): il CAPWAP viene usato solo per il traffico di control plane del WLC, invece il traffico dati è incapsulato in VXLAN tra i border e gli edge nodes (direttamente sull’ap). Il controller si occupa anche di mappare gli endpoint wireless passando le informazioni al control layer.
5.  Intermediate nodes

## QoS wired and wireless, components and policy
Introduzione a QoS: manipolazione e controllo delle code causate da saturazione di banda che vanno a causare latenza e jitter (“unstable latency”, meglio avere una latenza fissa che un latenza più bassa ma con degli spike di latenza). Il traffico va inizialmente classificato e successivamente va marchiato con un tag in modo che tutti gli altri apparati di rete possano fare policy in base a quel tag. La classificazione base è fatta con le porte, la classificazione più avanzata è fatta per application con NBAR o Network-Based Application Recognition.

**Come possiamo manipolare o condizionare il traffico e gestire le congestioni?**
1. Policing: il traffico in eccesso viene droppato.
2. Shaping: il traffico viene messo in una coda. La latenza può aumentare ma il packet loss rimane stabile o invariato. Applicabile anche lato client per gestire il traffico verso un provider che invece applica il policing ergo scarterebbe il traffico in eccesso.
3. La coda può essere gestita con:
	- Queuing: il traffico extra che supera il limite di bandwidth è fatto da diversi tipi di traffico perciò si creano code diverse con trattamenti diversi del traffico.
	- Scheduling
	- Congestion management: la coda può essere prevenuta gestendo il traffico che comincia a diventare un “picco” di traffico (RED, WRED).
	- Integrated services: RSVP, configurazione che va applicata su tutti i router allo stesso modo e tutti i router devono supportarla.
	- Differentiated services: ogni router ha un configurazione che può differenziare dagli altri apparati affinché il comportamento sia lo stesso o per-Hop behavior.

QoS policy: la MQC o Modular QoS Command-Line “way” è un template di QoS in configurazione a cui le interfacce degli apparati faranno riferimento.

Key topics:
- Policy map: mechanism to create a scheduler for packets prior to forwarding.
- Service policy: to apply a QoS policy to an interface.
- DSCP: portion of the IP header used to classify packets.
- CoS: portion of the 802.1Q header used to classify packets

WRED previene il degrado delle performance TCP perchè droppa pacchetti a caso ancora prima che ci sia congestione nelle code ed è la grossa differenza tra il policing e lo shaping che si attivano su un traffico congestionato.

## CEF, MAC, TCAM, FIB, RIB, switching mechanisms
The processing of the ingress to egress interface switching is done by the CPU (IP Input).

La CEF salva in cache RAM alcune decisione di forwarding ed è composta da FIB e adjacency table alleggerendo il carico della CPU. Le operazioni generate dal router verranno sempre processate in CPU. Nella CEF viene creato un hash della coppia ip sorgente e destinazione. La CEF di per sé non ha un numero preciso di percorsi da poter bilanciare.

CEF → FIB + adjacency table

Process switching → “punts” each packet where punt is the action of moving a packet from the fast path CEF to the route processor for handling.

FIB vs RIB:
- RIB - Routing information base - sh ip route ( shows rib- Where routing table is built- CONTROL PLANE). Le informazioni nella route table sono le informazioni rese “human readable” dalla RIB.
- FIB - Forward Information base - sh ip cef. È la routing table del CEF composta da coppie network + interfaccia d’uscita. 

Se una rotta cade viene ripopolata la RIB su cui si basa anche la FIB.

La Adjacency Table è dedicata alle informazioni in L2 come ARP, VLAN e MAC

TCAM vs MAC:
- Content Addressable Memory (MAC table) memoria in RAM per il Layer 2
- Ternary Content Addressable Memory (TCAM) memoria in RAM per il Layer 3

## GRE, IPSEC
Il Generic Route Encapsulation è un path PTP. Per ogni tunnel verranno create delle interfacce virtuali di tunnel.
A GRE tunnel is down with the error message `%TUN-5-RECURDOWN:Tunnel temporarily disabled due to recursive routing`. This condition is usually due to one of these causes. A misconfiguration that causes the router to try to route to the tunnel destination address using the tunnel interface itself (recursive routing) + A temporary instability caused by route flapping elsewhere in the network.

IPSec è un set di protocolli che garantisce: CIAA ovvero confidentiality, data integrity, authentication e anti-replay. Questo set deve essere condiviso tra due peer VPN. I parametri di hashing, encryption, PSKs sono scambiati tra i peer con delle security associations (SA).

Internet Key Exchange: “Dynamically configured with IKE”, posso configurare i parametri IPsec staticamente oppure lasciare che sia IKE a negoziare questi parametri con l’altro vpn peer dato un certo elenco di parametri obbligatori. La negoziazione si divide in due fasi:
1. Establish an authenticated tunnel: authentication (PSK), encryption (AES), hash (SHA), DH group, lifetime  
2. Negoziazione delle SAs: destination, data and transport method (tunnel or transparent)

Per prevenire la frammentazione sul tunnel GRE si usano:
- TCP MSS o TCP Maximum Segment Size: the maximum segment (chunk) of data that the TCP receiver was willing to accept.
- PMTUD o Path Maximum Trasmission Unit discovery

## LISP
- [LISP in a SDA world - BRKRST-2614](https://www.ciscolive.com/on-demand/on-demand-library.html?search=lisp&search=lisp#/session/16360599221560017jCj)
- [Solving the Network Location Problem with LISP Part 2 - 2013](https://blogs.cisco.com/perspectives/solving-the-network-location-problem-with-lisp-part-2)

LISP o Locator/ID Separator Protocol è il control plane del framework campus fabric. It tracks endpoints and networks in the fabric. Il protocollo LISP si occupa di “geolocalizzare un endpoint” e farlo comunicare in rete sempre con lo stesso ip a prescindere dalla sua posizione nella rete. Con il LISP si smette di segmentare le reti per locazione geografica ma per tipologia di traffico.

Il LISP si basa sulla mappatura degli endpoint ID o EID con il routing locator o RLOC. Ogni router in una rete LISP conosce unicamente le proprie rotte locali e per tutte le altre destinazioni interroga il mapping database centralizzato, il LISP map server o MS.

Un concetto alla base del LISP è “route to the location”. Una serie di ip pubblici che fanno parte di un unico service provider vengono riassunti in una location che riassumono il gateway per quel servizio, routing locator RLOC, (“upload ip”), le identities invece sono gli ip degli endpoint.

LISP LINGO
- PITR: proxy ingress tunnel router
- PETR: proxy egress tunnel router
- MS: map server
- MR: map resolver
- TLOC transport locator
- RLOC: routing locator
- ITR: ingress tunnel router
- ETR: egress tunnel router

## VXLAN
È il data plane del framework campus fabric. Il protocollo VXLAN incapsula il frame ethernet in pacchetti UDP per poter trasportare il traffico a layer 2 su reti IP layer 3 creando un tunnel di overlay. Questo protocollo permette di aggiungere all’informazione della vlan anche 64000 tag SGT che serviranno poi al Cisco TrustSec per applicare policy di QoS, PBR o di sicurezza basate su tag e non su indirizzamenti di rete.

VXLAN uses VXLAN tunnel endpoint (VTEP) devices to map tenants' end devices to VXLAN segments and to perform VXLAN encapsulation and de-encapsulation.