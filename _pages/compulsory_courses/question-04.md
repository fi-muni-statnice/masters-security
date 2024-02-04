---
title: Computer Networks
author: John Doe
category: Compulsory Courses
layout: post
---

$\def\cwnd{\text{cwnd}}$

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
  - the identifiers are [specified](http://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml){:target="_blank"} by IANA
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

#### Transmission Control Protocol (TCP)
- provides **connection-oriented** and fully **reliable** service
  - if possible, the data sent by the sender will be received by the receiver complete and in the right order
  - TCP is stream-based protocol in comparison with datagram-based UDP
- *connection* has to be established between sender and receiver prior to communication
  - *three-way handshake*

  ![tcp-handshake](/masters-security/assets/tcp_handshake.svg 'TCP handshake')
  - connection is distinguishable on the end nodes (end-to-end service), routers are not aware of the connections
  - an established connection might be used for full duplex communication
  - **point-to-point** connections only
- multiplexing/demultiplexing and error control same as in UDP

![tcp-header](/masters-security/assets/tcp_header.png 'TCP header')
- **source port**: identification of sending service/application
- **destination port**: identification of receiving service/applicatio
- **sequence number**: number assigned to the first byte of the data contained in the segment
- **acknowledgement number**: byte number the received is expecting to receive in the next segment
  - *piggybacking*: the acknowledgement is not sent immediately after receiving, but with the data that the other side wants to send
- **header length**: total length of the TCP header in 4B words
- **reserved**: not used for anything
- **control**: 6 control bits
![tcp-control-bits](/masters-security/assets/tcp_control_bits.png 'TCP control bits')
- **window size**: size of the windows that the other party must maintain, used for *Flow Control*
- **checksum**: checksum of the whole TCP segment
- **urgent pointer**: used when the segment contains urgent data
- **options**: [options](https://www.iana.org/assignments/tcp-parameters/tcp-parameters.xhtml){:target="_blank"} such as maximum segment size, selective ACK, authentication, etc.

TCP controls the amount of sent data in a way that
- *protects the receiver from being congested* (**Flow Control**)
- *protects the network from being congested* (**Congestion Control**)

The amount of data allowed to be sent to the network is defined by:
- the receiver's window size (flow control)
- the size of *congestion window* (congestion control), maintained on the sender size
- amount of data allowed to be sent to the network is limited by the **lower value** of these two parameters

*[TCP]: Transmission Control Protocol

### L7 - Application Layer
- provides services to *users*
  - application programs specific for a particular purpose (email, WWW, DNS)
- comprises *network applications/programs* and *application protocols*
  - application protocols (HTTP, SMTR, etc.) are parts of network applications (web, email)
  - application protocols define:
    - types of messages which the applications exchange (*request*/*response*)
    - message syntax
    - message semantics
    - rules of when and how the mesages are exchanged

*[HTTP]: Hypertext Transfer Protocol
*[SMTP]: Simple Mail Transfer Protocol

#### Application Classification/Distinction
*According to employed communication model*:
- Client-Server model - thin vs fat clients
- Peer-to-peer model

*According to the way of accessing the information*:
- pull model - the data transfer is initiated by a client
- push model - the data transfer is initiated by a server

*According to the demands on the computer network*:
- low vs high demands

#### Examples
- name service (DNS)
- World-Wide-Web (HTTP)
- electronic email (SMTP)
- file transfer (FTP)
- multimedia transmissions (RTP/RTCP)

*[FTP]: File Transfer Protocol
*[RTP]: Real-time Transport Protocol
*[RTCP]: RTP Control Protocol

# Advanced IPv6 Features
Hierarchy of a network IPv6 address (128 bits):
- first 16 bits contain the value `2001` (hexadecimal)
- next 16 bits assigned by *Regional Internet Registry*
- next 16 bits assigned by *Local Internet Registry*
- last 64 bits for the interface address

- IPv6 addresses are *classless* (classes do not exist, contrary to IPv4)
- networks defined using [CIDR](https://ip.sb/cidr/){:target="_blank"} notation

*[CIDR]: Classless Inter-Domain Routing

## Neighbor Discovery
Serves the same purpose as ARP for IPv4 - obtaining a physical address of a node, having its IP address

### Neighbor Discovery for IP version 6
- a part of ICMPv6
- autoconfiguration of IPv6 address (stateful/stateless)
- functionalities:
  - determine network prefixes, routers and other configuration information
  - duplicate IP address detection
  - determine L2 addresses the same link
  - detect changed L2 addresses
- five ICMP messages:
  - *Router Solicitation* (RS)
  - *Router Advertisement* (RA)
  - *Neighbor Solicitation* (NS)
  - *Neighbor Advertisement* (NA)
  - *ICMP Redirect*
- *Inverse Neighbor Discovery* possible (RARP equivalent)

Address resolution:
- a common multicast prefix is defined (`FF02:0:0:0:0:1:FF00::/104`)
- node looking for an L2-layer address takes last 24 bits of the IP address it is looking for and concatenates it with the prefix
- a **Neighbor Solicitation** message is sent to such a multicast address, containing the IPv6 address being resolved and the L2 address of the sending node
- the node belonging to the multicast group answers with a **Neighbor Advertisement** message, containing:
  - all the IPv6 and L2 addresses the node has
  - attribute:
    - *R* (*Router*) - the sender is a router
    - *S* (*Solicited*)
    - *O* (*Override*) - whether the new information should override the old information saved
- unsolicited NA is used when the node knows that its L2 address has changed, the messages are sent to multicast address containing all nodes (`FF02::1`)

### Autoconfiguration
- no need for a DHCP server
- two types of autoconfigurations
  - *stateful* - similar to DHCP, called DHCPv6
  - *stateless* -- new type of autoconfiguration
  - the two types may be combined

#### Stateless autoconfiguration
- routers inform periodically all nodes about the current configuration (**Router Advertisement**)
- a new node can also ask for RA by sending **Router Solicitation**
- router advertisement contain specific information about the router
  - MTU
  - network prefixes
  - L2 address of the router's interface

- to generate an IP address, a host uses a combination of local information (MAC address, random ID) and the information from router advertisements

1. **Link-Local Address Generation**: the device generates a link-local address (`FE80::<64-bit-identifier>`)
2. **Link-Local Address Uniqueness Test**: the node sends a NS and listens for a NA response (which would suggest a duplicate address)
3. **Link-Local Address Assignment**: the device assigns the link-local to its IP interface
4. **Router Contact**: the node attempts to contact a local router to obtain more information on continuing the autoconfiguration (either by listening to RA or sending an explicit RS to the all-routers multicast group `FF02::2`)
5. **Router Direction**: router provides instructions on how to proceed
   - either stateful configuration is in use, then it sends the address of a DHCP server
   - or it instructs the hosts how to determine its global internet address
6. **Global Address Configuration**: the host configures itself with its globally-unique address

*[NS]: Neighbor Solicitation
*[NA]: Neighbor Advertisement
*[RS]: Router Solicitation
*[RA]: Router Advertisement
*[DHCP]: Dynamic Host Configuration Protocol

## Mobility Support
- **main idea**: mobile devices have their home network
- used addresses:
  - *Home Address* - global unicast persistent address through which a mobile node is always accessible
  - *Care-of Address* - global unicast address whil the mobile node is in a foreign network
- *Correspondent Node* (CN) - a peer node with which a mobile node is communicating
- *Home Agent* (HA) - router in the home network through which the mobile node is always accessible
  - receives datagram destined to the mobile node and forwards them to the node via a tunnel
- *route optimization* - direct communication of the mobile and corresopnding nodes in order to optimize the communication
  - not necessary

Concept:
- mobile node at home receives packets through regular IP routing
- when away from the home network, the mobile node registers its care-of address with its HA
  - the care-of address is received via a mechanism in the foreign network
- two ways for a correspondent and mobile node to communicate
  - *bidirectional tunneling* - HA is the man in the middle
  - *route optimization* - binding has to be authorized through *Return Routability Procedure*

*[CN]: Correspondent Node
*[HA]: Home Agent

### Return Routability Procedure
- mobile node has to prove to CN that it owns both home address and care-of address
  - initially performed using IPsec, but there is no PKI available for the nodes
- the mobile node sends a *Test Init* message through HA to CN and another one directly to CN
- it receives *Test* messages back (one through HA, one directly) and uses them to compute shared secret with the CN - *Management Key*
- the *Management Key* is used to secure the Binding Update messages

### Home Agent
- maintains binding cache and a list of home agents
- processes bindings
- tunnels received packets to care-of address

*[PKI]: Public Key Infrastructure

## Security
- general security mechanisms described by IPSec both for IPv4 (**optional**) and IPv6 (**mandatory**)
- elements of IPSec:
  - authentication protocol - AH
  - encryption protocol - ESP header
  - definition for use of cryptographic algorithms for encryption and authentication
  - definition of security policies and security associations between communicating peers
  - key management

*[AH]: Authentication Header
*[ESP]: Encapsulating Security Payload

### Security Associations
- describes a secure connection between two devices
- three elements: key, encryption/authentication mechanism, additional parameters
- one-way agreements (4 SA needed for both encrypted and authenticated duplex communication)
- SA defined by three parameters
  - *Security Parameter Index* - unique identifier of the SA
  - *IP Destination Address* - address of the device
  - *Security Protocol Identifier* - specified whether the association is for AH or ESP
- to establish an SA the peers have to agree on the algorithm and the keys - *Internet Key Exchange version 2* used

*[SA]: Security Association
*[SPI]: Security Parameter Index

### IPSec Modes
- *Transport mode*
  - the protocol protects messages inside the IP datagram
  - message processed by AH/ESP, appropriate headers added in front of the transport header
  - IP header in front of all of this
- *Tunnel mode*
  - protects a **complete encapsulated IP diagram**
  - IPSec headers in front of the original IP header, new IP header added in front of all of this
  - the original IP datagram is therefore encapsulated inside the new IP datagram

### Authentication Header
- provides **authentication** of either all or part of the contents of a datagram
- addition of a header calculated based on the values in the datagram
- a SA has to be set up between the two communicated devices
- presence of AH allows to verify the integrity, but the datagram is **not encrypted**!

### Encapsulating Security Payload
- protects data by encrypting it: provides **privacy**
- supports additional **authentication scheme**
- ESP divides its fields into three components
  - *ESP Header* - two fields (SPI and Sequence Number)
  - *ESP Trailer* - placed after the encrypted data, contains padding used to align the encrypted data
  - *ESP Authentication Data* - if the authentication feature is used, this contains an *Integrity Check Value* (similar to AH)
- ESP uses stronger encryption algorithms than AH, so it is more computationally expensive
- AH authenticates the whole diagram, ESP does not authenticate the outer IP header

## QoS
Two architectures for providing data with priorities and quality guarantees:
1. *Integrated Services* - bandwidth and all related resources are reserved per flow on an end-to-end basis, routers store information about flows and analyze passing packets
  - application announces the qualitative requirements to the netwrok
  - the network checks whether the resources are available and either satisfies the request or refuses connection
  - main drawback: necessity to maintain a state in the inner network nodes
2. *Differentiated Services* - based on packets' markup (the packets contain a certain priority class)
  - resource reservation protocols not necessary
  - each packet has a tag indicating a priority class before being sent to the network
    - *Type of Service* (IPv4) or *Traffic Class* (IPv6) fields
    - packet processed based on the priority class
  - main advantage: simple, no state information in the inner nodes (good scalability)

### Traffic Class
- 1 byte
  - first 6 bits are the *DiffServ field* - specifies how packets should be forwarded
  - last 2 bits (*Explicit Congestion Notification*) - router can signal overload before a packet loss occurs

### Flow Label
A *flow* is a sequence of related packets sent from a source to a destination.

The **Flow Label** field is:
- a 20-bit field of IPv6 which enables classification of packets belonging to a specific flow
- IPv6 routers must handle all the packets belonging to the same flow in a similar fashion
- open issue how to use this field efficiently
- traditionally flow was based on the 5-tuple `(source address, destination address, source port, destination port, transport protocol type)`
  - some of these fields may be unavailable due to fragmentation/encryption
- IPv6 uses a triple `(flow label, source address, destination address)`
- a Flow Label of 0 is used to indicate packets not being part of any flow

# Routing
**Routing** is the process of finding a path in the network beteeen two communicating nodes.
- the route/path has to satisfy certain constraints
- influenced by several factors
  - *static*: network topology
  - *dynamic*: network load

## Path Vector Routing
- a variant of DV routing
- in comparison with DV whole paths are sent in the PV
  - simple detection of loops
  - definition of rules/policies (friendly vs. non-friendly AS)

### Border Gateway Protocol
- currently version 4
- proposed due to internet's grow and demands on complex topologies support
  - supports redundant topologies, deals with loops/cycles, ...
- used to communicate information about networks residing in an AS to other autonomous systems
  - exchange done by setting up a session between bordering AS
  - communication channel on top of TCP (BGP relies on a fully reliable transport protocol)
- allows a definition of routing rules (policies)
- a hop count metric
- CIDR for paths' aggregation
- BGP is based on *advertisements* sent among BGP peers
  - through TCP, port 179
  - advertisement consists of a destination network address (CIDR notation) and path attributes
- once paths are advertised to an AS, a *routing policy* takes place
  - routing policy defines which ASs are allowed to transit data, the allowed AS for forwarding, etc.
  - if a routing policy is not defined, the shortest path is chosen

*[DV]: Distance Vector
*[PV]: Path Vector
*[AS]: Autonomous System
*[BGP]: Border Gateway Protocol

## Router Architectures
Router must perform two tasks: *routing* and *packet forwarding*.
- **routing process** constructs a view of the network topology and computes the best paths
  - the paths are stored in the *forwarding table*
- **packet forwarding process** moves a packet from an input interface (ingress) to the appropriate output interface (egress)
  - based on the information contained in the forwarding table
  - performance of the forwarding process determines the overall performance of the router

### Forwarding Functions
- **IP Header Validation**: every IP packet needs to be validated - the version number, header length, checksum, etc.
- **Packet Lifetime ControL**: decrementing the TTL field, if the TTL is zero or negative, the packet is discarded and an ICMP message is generated
- **Checksum Recalculation**: header checksum needs to be updated
- **Route Lookup**: packet destination address used to search the forwarding table
- **Fragmentation**: if the MTU of the outgoing link is smaller than the size of the packet being transmitted
- **Handling IP Options**: a packet may indicate that it requires speecial processing at the router
- **Packet Classification**: examining other IP fields - source address, destination port, source port, etc.
- **Packet Translation**: router which is a gateway to a NAT network needs to support it
- **Traffic Prioritization**: router might need to guarantee a certain quality of service

*[NAT]: Network address translation

### Routing Functions
- **Routing Protocols**: different routing protocols (OSPF, BGP, RIP) for sending/receiving route updates
- **System Configuration**: functions enabling the operators to configure various administrative tasks
  - configuring the interfaces, keepalive time, rules for classifying packets, ...
- **Router Management**: monitoring (SNMP support)

### Router Elements
- *Network Interfaces*
  - a network interface contains many ports providing connectivity to physical network links (Ethernet)
- *Forwarding Engines*
  - responsible for deciding to which network interface the packet should be forwarded
  - consulting the *forwarding table* = **Address Lookup**
- *Queue Manager*
  - provides buffers for temporary storage of packets when an outgoing link from a router is overbooked
  - selectively dropping packets on buffer overflow
- *Traffic Manager*
  - responsible for prioritizing and regulating the outgoing traffic, depending on the desired level of service
- *Backplane*
  - provides connectivity for the network interfaces
- *Route Control Processor*
  - responsible for implementing and executing routing protocols
    - maintains a *routing table* that is updated when a route change occurs
    - *forwarding table* is computed based on the contents of the routing table
  - performs complex packet-by-packet operations (handling errors during packet processing)

*[SNMP]: Simple Network Management Protocol

### Address Lookup
1. Classful addressing: forwarding is straightforward
  - only the network part of the destination part needs to be examined
  - single entry for routing packets to all the hosts attached to a given network
2. Classless addressing (CIDR): more difficult
  - destination IP address does not carry the netmask information
  - prefixes in the forwarding table being matched with the destination address can have arbitrary length

#### Longest Prefix Matching
Linear search is too slow (linear), trie-based algorithms can be used.

Other approaches:
- search by length
- search by value
- hardware algorithms

## Traffic Engineering
Traffic Engineering is about discovering what other paths and links are available in the network, what the current traffic usage is within the network, and directing traffic to routes other than the shortest so optimal use of resources in the network is made.

Achieved by a combination of:
- extensions to existing IGP protocols
- traffic monitoring tools
- traffic routing techniques

*[TE]: Traffic Engineering

### Steps
1. traffic measurements are collected
  - SNMP, NetFlow
2. topology and configuration is obtained from the network
3. a link weight determination process determines linnk weights

How often to update the link weights?
- usually once a day, once a week - up to the administrator

*[IGP]: Interior Gateway Protocol

TE needs to solve constraint programming/linear programming problem of *minimizing the maximum link utilization*.

## Multiprotocol Label Switching
MPLS is a forwarding mechanism presented as a way of improving the forwarding speed of core IP routers.
- in MPLS network, packets are forwarded based on *labels*
  - a label is added in front of a packet
  - internal MPLS routers dont' inspect packets' IP addresses
  - labels usually correspond to IP destination networks
    - but can correspond to other parameters such as QoS or source address
  - new protocols to distribute label information
- MPLS flows are *connection-oriented* and packets are routed along pre-configured Label Switched Paths (LSPs)
  - LSP is unidirectional
- MPLS allows new forwarding paradigms not available with conventional IP routing
  - ability of network operators to dictate the path, VPN support, etc.
- standard technology for large-scale IP networks

*[MPLS]: Multiprotocol Label Switching
*[LSP]: Label Switched Path
*[VPN]: Virtual Private Network

### Basic functionality
- analysis of packets entering the network and their classification to FEC classes
- labels' creation for all the FEC classes
- determination/creation of *Label Switched Paths*
- labels' distribution
- setting the forwarding information tables in the routers
  - *Label Information Base* (LIB) or *Label Forwarding Base* (LFB)
  - tables map `{incoming_interface, incoming_label}` to `{outgoing_interface, outgoing_label}`
- packets' forwarding based on the label
- MPLS header (shim header) creation

*[FEC]: Forward Equivalence Class

### Network Components
#### Edge Label-Switched Routers
- border routers
- *Ingress-LSR*:
  - analyses information in IP packet header
  - based on analysed information the packet is assigned to particular FEC
  - depending on the assigned FEC, a proper label is inserted into the MPLS header
- *Egress-LSR*:
  - removes MPLS header and forwards the original IP packet to an egress link
  - decrements packet's TTL field

*[LSR]: Label-Switched Router

#### Core Label-Switcher Routers
- ensure packets' forwarding based on the assigned label
- the IP header is neither modified nor analysed by the Core-LSRs, just the MPLS labels

### Traffic Engineering in MPLS
- MPLS has the ability to establish an LSP that follows an arbitrary path (not the path preferred by the routing protocol)
- resources inside the MPLS network can be dynamically reserved and updated
  - guarantee of QoS for traffic flows
- load-sharing and traffic grooming are made just at the entry point into the LSP

# TCP and TCP Variants
Traditional TCP is not suitable for network links with *high capacity* and *high latency*.

## Traditional TCP
Final window is the minimum of the flow control window (`rwnd`, deterministic) and the congestion control window (`cwnd`, estimate)

$$\text{ownd} = \min\{\text{rwnd}, \cwnd\}$$

The bandwidth $\text{bw}$ can be computed as

$$\text{bw} = \frac{8 \cdot \text{MSS} \cdot \text{ownd}}{\text{RTT}}$$

where MSS is the maximum segment size and RTT is the round-trip time.

### Congestion Control
- *AIMD - Additive Increase Multiplicative Decrease* approach
- TCP **Tahoe**:
  - $\cwnd = \cwnd + 1$ per RTT without loss (above $\text{ssthresh}$)
  - $\text{sstresh} = \cwnd/2, cwnd = 1$ per every loss
- TCP **Reno** adds:
  - *fast retransmission*
    - a TCP receiver sends immediate duplicate ACK when an out-of-order segment arrives
    - loss indicated by 3 duplicate ACKs
      - once received, TCP performs a fast retransmission without waiting for the retransmission timer to expire
  - *fast recovery* - slow start phase not used anymore
    - $\text{sstresh} = \cwnd = \cwnd/2$

## Multi-stream TCP
- assumes multiple TCP streams transferring a single data flow
- improves TCP's performance/behavior in cases of *isolated packet losses*
- simple implementation: bbftp, GridFTP, Internet Backplane Protocol, ...
- drawbacks:
  - more complicated than traditional TCP (more threads necessary)
  - startup accelerated linearly
  - leads to *synchronous overloading of queues and caches in the routers*

## TCP Implementation Tuning
- *cooperation with hardware*
  - Rx/Tx TCP Checksum Offloading
- *zero copy*
  - user-land <-> kernel <-> network card usually leads to multiple copies of the pacets
  - page flipping - user-land <-> kernel data movement

## Conservative Extensions to TCP
### GridDT
- a collection of ad-hoc modifitactions
- just the sender side has to be modified
- updated congestion control
- faster slowstart

### High-Speed TCP (HSTCP)
- congestion control is AIMD/MIMD:
  - $\cwnd = \cwnd + a(\cwnd)$ per RTT without loss
  - $\cwnd = \cwnd + \frac{a(\cwnd)}{\cwnd}$ per ACK
  - $\cwnd = b(\cwnd) \cwnd$ per packet loss
- emulates the behavior of traditional TCP for small window sizes and/or higher packet loss rates in the network
- doesn't deal with the slow-start phase in a sophisticated way

*[AIMD]: Additive Increase Multiplicative Decrease
*[MIMD]: Multiplicative Increase Multiplicative Decrease

### BIC-TCP
- was the default for Linux kernels, replaced by CUBIC-TCP
- binary-search algorithm for the $\cwnd$ update
- 4 phases:
  1. reaction to packet loss
  2. additive increase
  3. binary search
  4. maximum probing

*[BIC]: Binary Increase Congestion

### CUBIC-TCP
- BIC's growth function considered to be still aggressive for TCP
- default TCP congestion algorithm for the Linux kernel and Windows 10/11 since 2017
- uses a cubic function

### TCP BBR
BBR is model based like TCP Vegas.
- used for TCP traffic from google.com and youtube.com
- supposedly has fairness issues to non-BBR streams

*[BBR]: Bottleneck Bandwidth and Round-trip propagation time

### Approaches Different from TCP
#### tsunami
- TCP connection for out-of-band control channel
  - parameter negotiation, retransmissions - NACK
- UDP channel for data transmission
  - MIMD congestion control
  - highly configurable/customizable