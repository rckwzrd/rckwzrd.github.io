---
layout: post
excerpt_separator: <!--more-->
---

Notes on networking. The absolute basics.

<!--more-->

Based on CompTIA A+ 220-100.

[Wikipedia OSI Model](https://en.wikipedia.org/wiki/OSI_model)


## Hubs and Bridges

Hubs and bridges are less common these days due to low cost of higher level devices.

Hubs connect computers. 

Bridges connect networks segments and nodes.

Hubs

- OSI level 1 (physcial level)
	- no logic or software
- simple device to connect computers
- recieved signals copied to all other ports
	- creates noise
- active
	- signal boosting and repeating
	- extend length of network
- passive
	- no boosting or extension

Bridges

- OSI layer 2 (physcial address aware)
	- mac address is the physical hardware address
	- logic to bridge traffic between nodes with mac
- bridge functions
	- join similiar network topologies
	- divide network into segments or nodes
	- isolate network traffic between nodes
- bridges forward broadcast packets
	- broadcasts addressed to all computers
- cannot perform intelligent network path selection
	- route between sender and destination is constant


## Switches

Switches connect computers. It overtook the hub.

- OSI layer 2 (physical address aware)
	- use mac to determine where to send data
- provides central connectivity between computers
- examine layer 2 header (mac) for info
- uses info to forward packets to correct port
	- reduce noise and improves performance
	- builds mac address table
	- filters traffic
- advantage
	- increase network bandwidth, security, and performance
	- regulates flow of traffic and reduces packet collissions
	- mac address are known for all hardware
- disadvantage
	- difficuly to troubleshoot
	- devices can be spoofed
	- needs proper design and config
- managed (intelligent switch)
	- configure over IP with software and dedicated port
- unmanaged (plug and play)
	- no configuration control
	- cost efficient for small deployments


## Routers

Routers connect networks. It overtook the bridge.

Intelligent devices that find the best path for transmitting data between networks using IP addresses. Routers are required to connect seperate WANs and LANs. 

- OSI layter 3 (networks)
	- IP address
	- LAN, WAN, copper, and fiber
	- route table programming
- routing tables store network addresses
	- initial routes
	- routing tables can be shared between routers
- transmit accross and connect multiple networks
	- subnets in a large network
- does not forward broadcasts
	- isolated broadcast domains
- determine best route accross known routes
	- consider distance and congestion

## Access Points, Repeaters, and Extenders

- access point
	- any point that enables access to network
	- "wireless access point"
	- "ethernet port in a wall outlet"
- repeaters and extender
	- operate at layer 1
	- improve signal range
	- extend wifi network

## Network Controllers

Network interface cards are used to connect hardware to a switch or network.

- network devices require interface
	- wireless or wired
- can be hardwired or modular
- speed and duplex
	- set with auto negotiate
- wake on LAN
	- turn on hardware with network message

## Cable and DSL Modems

Hardware devices used to connect to a remote netowrk or internet.

Signal is transmitted in an analog or digital form over long distances. Contraction of modulate and demodulate.

- send and recieve data
	- telephone and cable lines
- dial-up modem
	- dial into ISP via telephone
	- slow speed 56kbs
- digital subscriber line (DSL)
	- transfer digital signal over standard telephone
	- use DSL modem to connect to ISP
- cable modem
	- coax tv lines to connect to ISP
	- always on and fast data transfer

## Patch Panels

Structured cabling, patch panels, and network racks.

- structured cabling
	- bundling and organizing cables
	- safety and aesthetics
- patch panel
	- hardware assembly with many ports
	- manages connections to LAN
	- types based on ports and cable specs
- network racks
	- chassis holding patch panels, switches and router

## Power over Ethernet

Used ethernet cable to carry electrical power. Can place a LAN in area that does not have power.

- common for wireless access points
- IEEE 802.3bt next gen POE
	- cameras
	- LED
	- terminals

## Ethernet over Power

Transmit data using common electrical wiring.

Ideal when it is not possible to run cables or extend WIFI.

- requires outlet and adapter

## Firewalls

Allow or deny conenctions to a network based on a set of rules.

- allow or deny connection with rules
- rules are based on IP addresses and ports
- stateful filters maintain session state info
- protect against outside threats
- hardware
	- placed between LAN and internet
	- port and IP rules
	- act as content filter, VPN concentrator, honeypot
- software
	- runs on host as an app
	- control internet access over ports
	- is host is compromised so is the firewall
- content filters
	- component of a firewall
	- analyze packets and allow/deny with rules
	- common filters include executable, emails, websites

## Cloud Network Controllers 

Component that controls the flow of traffic from access points through networks. The network controller configures and manages all access points.

- on premise or cloud controler backhaul
	- communinication is tunneled back to controller
	- control plane contains instructions and rules
	- data plane is acutal traffic
- cloud managed wireless LAN
	- virtual controller
	- located in public cloud
	- connects remote LANS
	- controller software and traffic accessed over the web
