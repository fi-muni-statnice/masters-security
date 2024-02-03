---
title: Computer Networks
author: John Doe
category: Compulsory Courses
layout: post
---

Computer networks. Network models and their layers, their functionality, interaction, standardization. Protocols at network layer, IPv4 features, advanced IPv6 features, address space. Routing: routers and their architecture, routing algorithms, families of routing algorithms, multicast routing, MPLS, TE. Transport protocols: UDP, properties of TCP and its variants, transport protocols for high-speed networks. (PA191)

# Computer Networks
- a group of computers and devices interconnected by communication channels that facilitate communications among users and allows users to share resources
- purposes: facilititating communications, sharing hardware, files, data, information, software
- characteristics: *delivery*, *accuracy*, *timeliness*

> **Network Protocol** is a set of rules that defines the format and the order of messages exchanged among two or more communicating entities, as well as the actions performed during sending/receiving the messages.
{: .block-tip}

## Standardization
- definition of norms/standards describing various actions, activites, forms/methods of communication

Goals:
- quality
- security
- compatibility
- interoperability
- portability

Standards fall into two categories:
- *de facto*: have not been approved by an organized body but have been adopted as standards through widespread use
- *de jure*: standards legislated by an officially recognized body

Standard IT organizations:
- ISO, ITU-T, ANSI, IEEE, IETF (RFC), IEC, ...

*[ISO]: International Organization for Standardization
*[ITU-T]: International Telecommunication Union Telecommunication Standardization Sector
*[ANSI]: American National Standards Institute
*[IEEE]: Institute of Electrical and Electronics Engineers
*[IETF]: Internet Engineering Task Force
*[RFC]: Request for Comments
*[IEC]: International Electrotechnical Commission
*[OSI]: Open Systems Interconnection

# Network Models
## ISO/OSI Model
- **7-layer model** proposed by OSI in order to ensure compatibility and interoperability of communication systems developed by various vendors.
- layered architecture
  - each layer is **responsible for particular functionality**
    - adds control information to the data in order to do its job
  - each layer **communicates only with its neighbours**
    - uses the services provided by the lower layer, provides its services to the upper layer
    - functionality **isolated** in the particular layer
  - logically, communication performed between peer layers; physically the communication has to traverse all the lower layers
- 7 layers not widely accepted, leading to the TCP/IP model

![network-models](/masters-security/assets/network_models.png 'ISO/OSI Model vs. TCP/IP Model')

## TCP/IP Model
### L1 - Physical Layer
- Provides the functionality for an interaction with transmission media.
- Provides services for the *Data Link Layer*
  - the Data Link Layer passes/obtains data to/from the Physical Layer in the form of 0s and 1s organized into **frames**
  - the Physical Layer transforms the streams of bits (from frames) into *spread* through the transmission media
- controls the transmission media:
  - sending/receiving the data (signals)
  - data transformation (coding) into signals
  - the number of logical channels simultaneously transferring data from various sources

**The main goal**: ensure a transmission of bits between sender and receiver

- several standards (RS-232-C, IEEE 802.x) defining electrical, mechanical, functional and procedural characteristics of interfaces used for connecting various transmission media and devices

#### Services
- bit-to-signal transformation
- bit-rate control
- bit synchronization
- multiplexing
- circuit switching

#### Signals
- *signal* = function of time representing changes of physical (electromagnetic) characteristics of the transmission media
- data that have to be transferred are 0s and 1s - digital
- the transmission media is suitable for either analog (requires *modulation*) or digital (requires *transcoding*) transmission
  - both analog and digital: wired media, optical fibre
  - only analog: ether (air)

#### Multiplexing
A technique of sharing the available bandwidth by concurrent communication channels. The goal is to maximize the utilization of the media, it is applicable especially for optical fibres and non-wired media.

For analog signals:
- frequency-division multiplexing (FDM)
- wave-division multiplexing (WDM)

For digital signal:
- time-division multiplexing

### L2 - Data Link Layer
- receives *packets* from the Network Layer and transforms them into *frames*
- in cooperation with the Physical Layer it ensures the transmission of frames between communicating devices interconnected with a *(shared) transmission media*
  - local delivery (LAN)
- ensures the transmission reliability between these devices
- ensures the flow control in order to avoid receiver congestion
- controls the access of the devices to shared media (Medium Access Control)

*[LAN]: local area network

#### Services
- *Framing*: the incoming packets are encapsulated into *frames*
- *Addressing*: provides the addresses of physical layer entities - *physical/MAC addresses*
- *Error Control*: ensures the required reliability of the data link (error detection, correction)
- *Flow Control*: prevents the receiver congestion, *stop-and-wait*, *sliding-window* mechanisms
- *Medium Access Control*: eliminates collision caused by multiple concurrent transmisisons

#### Error Control
- concept of redundancy
- Error Detection, ARQ: even/odd parity, CRC, etc.
- FEC: both error detection and correction using redundant data, for example using Hamming codes

*[ARQ]: Automatic Request for Retransmission
*[CRC]: Cyclic Redundancy Check
*[FEC]: Forward Error Correction

#### Medium Access Control
The functionality responsible for coordination of multiple devices' access to shared transmission media.

*The goal*: elimination of collisions caused by concurrent transmissions

Protocols:
- *random-access protocols*: Aloha, CSMA/CD, CSMA/CA
- *controlled-access protocols*: based on reservation/polling/tokens/...
- *channelization protocols* - FDMA, TDMA, etc.

*[CSMA/CD]: Carrier Sense Multiple Access/Collision Detection
*[CSMA/CA]: Carrier Sense Multiple Access/Collision Avoidance
*[FDMA]: Frequency Division Multiple Access
*[TDMA]: Time Division Multiple Access

#### Networks
- local area networks (LANs)
  - systematic topology for simple networks: bus, circle, star, tree, mesh
  - wider networks composed by interconnecting simple topologies
- L2 interconnection devices
  - bridge: transparent network interconnection (all the traffic passes the bridge), separates shared media (collisions do not pass the bridge)
  - switch: multi-port bridge
- based on MAC addresses
  - *Backward Learning Algorithm*: bridge learns the locations of the network nodes by listening on the media
    - frames are switched based on the receiver address
- characteristics:
  - it's possible to create networks with cycles: *Distributed Spanning Tree Algorithm* is used for preventing forwarding packets in a loop, thus ensuring safe flooding

##### Distributed Spanning Tree
The goal is to disable some bridges' ports to prevents loops.
- every bridge sends reports in the form of `<MAC, root bridge MAC, cost of the path to the root>`
- once a bridge receives a report, it updates its information about the best path
  - prefers the root with lower address
  - prefers lower path consts
  - in the case of same cost it prefers lower address
- mechanism:
  - root bridge selection (it has the lowers address)
  - sequential growth of thre tree
  - best paths found define the active ports
  - other ports are disabled

1. *root bridge selection phase*
- all bridges claim themselves as *root bridges*
- the root bridge is selected as the lowest address

2. *root ports selection phase*
- each bridge chooses its *root port* - the port with the lowest cost to the root bridge
- if two ports have the same cost, the one with lower ID of the port is selected

3. *active/inactive ports selection phase*
- *Root Bridge* sets all its ports as active
- The bridges communicate via all the links not containing root ports and try to determine the bridge with the lower bridge ID. The lower bridge ID sets its port as active, the other turns off the port.

*[MAC]: medium access control

### L3 - Network Layer
- provides services for the *Transport Layer*
  - receives *segments* from the Transport Layer and transforms them into *packets*
  - in cooperation with the Data Link Layer ensures the packets' transmission between communicating nodes
- logically joins independent LANs
  - the upper layers are provided with an illusion of just a single WAN
- allows unique identification (addressing) of every host/device on the Internet
- ensures *routing* of passing packets
- in cooperation with the Data Link Layer associates the L3 addresses (IP) with the L2 addresses (MAC) and vice versa

#### Services
- *Internetworking*: logical gluing of physical networks together to look like a single network (the internet)
  - an illusion of a uniform environment provided by a single WAN
- *Packetizing*: received segments are transformed into packets
- *Fragmenting*: when the datagram is larger than the MTU of the network, it is divided into smaller fragments sent separately
- *Addressing*: IP addresses unique throughout the whole network
  - packets contain source and destination addresses of communicating entities
- *Address Resolution*: ARP, RARP protocols
- *Routing*: selecting paths in a network along which to send network traffic from a source to a particular destination
- *Control Messaging*: basic information about unavailability to deliver a packet, network/host state, etc. (ICMP)

*[WAN]: wide-area network
*[MTU]: maximum transmission unit
*[ARP]: Address Resolution Protocol
*[RARP]: Reverse Address Resolution Protocol
*[ICMP]: Internet Control Message Protocol

#### IPv4 Addresses
32-bits represented most commonly in the format 123.123.123.123.

- *Unicast Address*: an identification of a single network interface
- *Broadcast Address*: data sent to all the hosts on a particular LAN
- *Multicast Address*: identification of a group of receivers who **applied for the data**
  - routers send such data to all the group members

#### IPv6 Addresses
128 bits, a hexadecimal notation in pairs of bytes separated by colons. **Leading** zeros might be omitted in every group, consecutive groups of zeros might be omitted and replaced by the `::` symbol (only once in an IPv6 address).

- *Unicast Address*: single network interface
- *Multicast Address*: a group of receivers
  - prefix `ff00::/8`
- *Anycast Address*: a group of receivers, but the data are delivered only to the closest member of such a group

#### Internet Protocol v4
- ensures datagram delivery through intermediate nodes (routers) - *host-to-host delivery*
  - hosts/interfaces identified by their IP addresses
  - *datagram approach* to packet switching, communication is connectionless
- unreliable (*best-effort*) service
- supplemented by a set of supporting protocols (ICMP, ARP, RARP, IGMP)

![ipv4-datagram](/masters-security/assets/ipv4.png 'IPv4 Datagram')

- **Version (VER)**: IP version
- **Header length (HLEN)**: length of IP datagram header in 4B words
  - the *Option* field makes the length of the header variable
- **Differentiated services (DS)**: defines the class of the datagram for QoS purposes
- **Total length**: the length of the whole IP datagram in bytes
- **Identification, Flags, Offset**: used for fragmentation
- **Time to live (TTL)**: maximum number of hops visited by the datagram
  - each router decrements the number by 1
  - when TTL = 0, the datagram is discarded
- **Protocol**: higher-level protocol identification
  - the identifiers are [specified](http://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) by IANA
    - e.g., 1 = ICMP, 2 = IGMP, 6 = TCP, 17 = UDP, etc.
- **Header checksum**: checksum of the header (not the data)
  - has to be recomputed on every router because of the TTL field
- **Source/Destination IP address**: 32-bit, identify sender/receiver
- **Option**: mostly network testing and debugging
- **Data**: the data being transferred

*[IGMP]: Internet Group Management Protocol
*[QoS]: quality-of-service
*[IANA]: Internet Assigned Numbers Authority

#### ICMP
- a supplementary protocol for IP
- provides information about errors in data delivery and basic information about the network state
- examples:
  - *Destination unreachable*
  - *Time exceeded* - TTL expiration
  - *Echo request/reply* - ping
- encapsulated in an IPv4 packet

#### Internet Protocol v6
Features compared to IPv4:
- *larger address space* - 128-bit addresses
- *simpler header format* - 40B header with the most important information
- *allowance for extension* - *extension headers*
- *support for real-time transfers* - flow tagging, flow priorities
- *mobility support* - via *home agents*
- *device autoconfiguration support* - stateful and stateless autoconfiguration

![ipv6-datagram](/masters-security/assets/ipv6.png 'IPv6 Datagram')

- options and fragmenting information moved to extension headers
- checksum removed (ensured on L2 and L4)
- **Version (VER)**: IP version (6)
- **Priority (PRI)**: also called *Traffic Class*, datagram priority with respect to traffic congestion
- **Flow label**: designed to provide special handling for a particular flow of data, not used
- **Payload length**: total length of the IP datagram excluding the base header
- **Next header**: defines the header that follows the base header (extension header or transport header)
- **Hop limit**: equivalent to TTL
- **Source/Destination address**: IPv6 addresses

![ipv6-extension-headers](/masters-security/assets/ipv6_extension_headers.png 'IPv6 Extension Headers')
- several extension headers have been defined - Hop-By-Hop Options, Routing, Fragment, Encapsulating Security Payload, Authentication Header, etc.

#### ICMPv6
- based on the same mechanisms as ICMP
- includes the functionality of ARP and IGMP using the *Neighbor Discovery* protocol

#### Routing
It is the process of finding a path in the network between two communicating nodes.
- the route/path has to satisfy certain constraints
- influenced by static (network topology) and dynamic (network load) factors

The global knowledge of network topology cannot be obtained - it's too big and too dynamic.

- a local view of network topology represents a *routing table*
- the difference between local and global knowledge can lead to
  - cycles/loops
  - oscillation

Main goal of routing is:
- to find optimal paths - optimality is a *metric*
- deliver a data packet to its receiver

- routing does not deal with the whole packet path, just a single step (**hop-by-hop** principle)

Required features for routing algorithms:
- accuracy
- simplicity
- effectivity and scalability
- robustness and stability
- fairness
- optimality

##### Distributed Routing
- *Distance Vector (DV)* - Bellman-Ford algorithm
  - neighboring routers periodically exchange complete copies of their routing tables
  - based on the content of received updates, a router updates its information and increments its distance vector number
  - "all information about the network only to the neighbors"
  - RIP: number of hops used as a metric, information sent every 30 seconds
- *Link State (LS)* - Dijkstra algorithm
  - routers periodically exchange information about states of the links they are connected to
  - every router maintains complete information about the topology
  - Dijkstra is used for shortest path computation
  - OSPF: metric is cost, by default derived from the link's throughput (the lower the cost), the better the link is
    - extensions: message authentication, routing areas, load-balancing

*[CIDR]: Classless Inter-Domain Routing
*[RIP]: Routing Information Protocol
*[OSPF]: Open Shortest Path First

##### Multicast Routing
Two approaches: *Source Based Tree* and *Core Based Tree*.

| **Source Based Tree** | **Core Based Tree** |
| top-down activity     | a core is established - ensured by meeting points (MP) |
| periodic broadcast    | a client contacts an MP |
| cutting the subtrees with no clients | down-top activity (from the receiver) |
| wideness restriction - TTL | reduces broadcast → better scalability |
| drawbacks: overhead, flooding | drawback: dependence on the core availability |
| protocols: DVMRP (RIP), MOSPF (OSPF) | protocols: CBT, PIM-SM

*[DVMRP]: Distance Vector Multicast Routing Protocol
*[MOSPF]: Multicast Open Shortest Path First
*[CBT]: Core-based trees
*[PIM-SM]: Protocol Independent Multicast - Sparse Mode

### L4 - Transport Layer
- provides its services to the *Application Layer*
  - obtains data coming from the sending application and transforms them into *segments*
  - delivers received segments to the destination application
- in cooperation with the network layer ensures data (segments) delivery between communicating applications/processes
  - providing transmission reliability if required (TCP)
  - logical communication channel - illusion of direct physical interconnection
  - *process-to-process* delivery
- it is the lowest layer providing *end-to-end* services
  - routers are not interpreting the transport layer data

#### Services
- *Packetizing*: data provided by an application are transformed into packets
- *Connection Control*: *connection-oriented* and *connectionless* services
- *Addressing*: addresses of transport layers entities are called *ports* - 16-bit numbers
  - packets contain source and destination ports
  - application is uniquely identified by the tuple `(IP address, port, transport protocol)`
- *Connection Reliability*: flow control and error control provided on the *end-to-end* principle
  - ensures reliability over the *best-effort* service provided by IP
- *Congestion Control, QoS* ensurance

- **Connection oriented services**
  - prior to the transmission, a connection is established
  - packets are numbered, their delivery is explicitly acknowledged
- **Connection-less services**
  - packets sent to the destination application without any connection being established
  - packets not numbered or acknowledged
    - might be lost, delayed, out-of-order

#### User Datagram Protocol
- the simplest transport protocol providing a **connection-less** and **unreliable** service
  - *best-effort*
  - simple error control
  - reliability has to be provided by the application layer if needed
- *main features*: simplicity, minimal overhead
  - no connection establishment/maintenance
  - no state information
  - simple header
- applications:
  - request-reply communication (DNS)
  - processes with internal flow and error control (TFTP)
  - real-time transfers
  - multicast transfers

*[UDP]: User Datagram Protocol
*[DNS]: Domain Name System
*[TFTP]: Trivial File Transport Protocol

![udp-header](/masters-security/assets/udp_header.png 'UDP header')
- **source port**: the identification of sending service/application
- **destination port**: the identification of receiving service/application
- **length**: the total length of the UDP packet (header + data)
- **checksum**: the whole UDP packet checksum (header + data)

### L7 - Application Layer


##### MPLS
##### TE

*[MPLS]: Multiprotocol Label Switching
*[TE]: Traffic Engineering