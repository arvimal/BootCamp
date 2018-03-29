Networking course
=================

Day 1
-----

### Fundamentals

* What is networking? - Sending data from one place to another
* What is data? - Ones and zeroes which represent something
* What is a protocol? - Messages in a pre-defined format

### History

''http://en.wikipedia.org/wiki/Computer_networking#History''

* 1950s - individual computers, electronically separate, all with their own message format, no common protocol
* Circuit switching vs Packet switching
* ARPAnet (1969) - research network funded by US DoD for linking universities, designed to handle loss of network segments, email and FTP were developed to work over it
* There were other research networks active around this time too, CYCLADES (French govt-sponsored public research) and NSFNET (US govt-funded National Science Foundation) are two important ones
* There was seen a need to standardize protocols. Taking ideas from these research networks, Vint Cerf and Robert Kahn developed TCP/IP which gradually became the standard.

### Present

* Most communication is TCP/IP over packet-switched Ethernet networks
* It seems common to shoehorn new technologies into existing moulds (Ethernet, Gigabit Ethernet) rather than learn new technologies (ATM, Infiniband)
* As well as build additional standards and fixes and additions onto existing technologies (eg: TCP and all its related options like Window Scaling, Timestamps, etc)
* On the internet we've exhausted IPv4 address space - ''http://imgs.xkcd.com/comics/map_of_the_internet.jpg'' - IPv6 penetration is only a few percent
* Is the internet unregulated "for all", or is it controlled by corporate/political entities?
* Privacy and encryption concerns, mostly around social networking and the NSA

### Future

* More bandwidth
* IPv6 - real soon now!
* The "Internet of Things" - Smart World infographic
* Virtualization, Cloud Computing, Software-Defined Networking (Open vSwitch)
* Loss of net neutrality?

### OSI Model

''http://en.wikipedia.org/wiki/OSI_model''

* Layer 1 - Physical - plugs, cables, network interface cards, switchports, backplanes
* Layer 2 - Data link - MAC addresses - switching - switches, bridges, VLANs
* Layer 3 - Network - IP addresses - routing - routers
* Layer 4 - Transport - TCP/UDP connections
* Brief overview of remaining layers
* Layer 5 - Session - eg: RPC, SSL
* Layer 6 - Presentation - eg: ASCII, XML
* Layer 7 - Application - the data

''http://en.wikipedia.org/wiki/OSI_model#Examples''

* TCP/IP Model (DoD Model) and correspondence to OSI model

''http://en.wikipedia.org/wiki/Internet_Protocol_Suite#Layers_in_the_Internet_protocol_suite''

''http://en.wikipedia.org/wiki/OSI_model#Comparison_with_TCP.2FIP_model''

* Computer Network Training Course 2.1 - OSI Model (5 minutes) ''http://www.youtube.com/watch?v=6uR3KAWvBrc''

* Computer Network Training 2.2 - OSI Model (8 minutes) ''http://www.youtube.com/watch?v=WyG9xL0Yraw''

* Computer Network Training 2.3 - OSI Model (8 minutes) ''http://www.youtube.com/watch?v=8IFTtAR6nOs''

* Computer Network Training Course 2.4 - OSI Model (9 minutes) ''http://www.youtube.com/watch?v=Unu31ivPXwM''


### Layer Data Formats

Each layer has its own Protocol Data Unit (PDU) in a specific format.

Think of each layer as a "wrapper" around the above layer's PDU, like an onion.

Each Layer has a specific name but "packet" ends up being used for every layer. The name doesn't really matter, as long as you are talking about the same thing. You might find it easier to clarify with the type of PDU being used, eg: "IP packet" or "TCP packet".

#### Layer 2 - Data Link

The Layer 2 PDU is called a "frame".

* ''http://en.wikipedia.org/wiki/Ethernet_frame''
* ''http://wiki.wireshark.org/Ethernet''

#### Layer 3 - Network

The Layer 3 PDU is actually called a "packet".

* ''http://en.wikipedia.org/wiki/IPv4_header#Header''
* ''http://www.tutorialspoint.com/ipv4/ipv4_packet_structure.htm''

#### Layer 4 - Transport

The Layer 4 PDU is called a "TCP segment" or "UDP datagram" or "SCTP packet".

* ''http://en.wikipedia.org/wiki/Transmission_Control_Protocol''
* ''http://en.wikipedia.org/wiki/User_Datagram_Protocol''

### L2/L3 - Broadcast Domains and Subnetting

* Subnetting, Cisco CCNA, Binary Numbers - Part 1 - ''http://youtu.be/Jv3PxtrIbMs'' (7 mins)
* Subnetting, Cisco CCNA, Binary Numbers - Part 2 - ''http://youtu.be/GN4GypAU7Sc'' (5 mins)
* Subnetting, Cisco CCNA, Binary Numbers - Part 3 - ''http://youtu.be/p8Y6xymAJK0'' (4 mins)
Subnetting, Cisco CCNA, Binary Numbers - Part 4 - ''http://youtu.be/6H1b3WRymbU'' (6 mins)

* Subnetting Cisco CCNA - Part 1 The Magic Number - ''http://youtu.be/a84XIopJFXs'' (9 mins)
* Subnetting Cisco CCNA - Part 2 The Magic Number - ''http://youtu.be/84-zNmomYzk'' (6 mins)
* Subnetting Cisco CCNA - Part 3 The Magic Number - ''http://youtu.be/-kAhTal4bNk'' (10 mins)
Subnetting Cisco CCNA - Part 4 The Magic Number - ''http://youtu.be/RGBA83J60H0'' (9 mins)


#### VLANs

"Virtual LANs" are a way of separating traffic at Layer 2, but not separating at Layer 1.

This is accomplished by inserting a small "tag" inside the Layer 2 header.

VLANs are used for:

* Dissimilar traffic sharing the bandwidth of one link
* Separation of unrelated traffic (eg: different departments, different customers)
* Traffic control or "Quality of Service" (QoS) of different traffic types (eg: prioritize voice over web browsing)

VLANs are defined by IEEE standard 802.1q, so is often called "dot 1 Q".

''http://en.wikipedia.org/wiki/IEEE_802.1Q''

The field for VLAN ID is 12 bits wide, so can support 4096 VLAN numbers, though 0 and 4096 are reserved. You might often hear VLANs >1024 referred to as "Extended VLAN", this is because early Cisco equipment only supported 1024 VLANs.

An untagged frame is in the "Native VLAN" of the port, usually this is VLAN1.

A port which can carry VLAN-tagged traffic is called a "trunk port". A trunk port can have a list of allowed VLANs, or it can allow all VLANs.

A port which resides in a VLAN is called a "switchport".

Think of the VLAN like a layer around a packet which stops IP talking to unrelated subnets. The layer must be added and removed at some point. Understanding the point of tag insertion/removal can make VLANs easier to understand.

You can double-tag VLANs, defined by IEEE standard 802.1ad but usually called "Q-in-Q" or "Nested VLAN".

Day 2
-----

### L2 - Data Link Layer - Switching

* A device like a switch uses MAC address information stored in a table (Forwarding Information Base) to make decisions about which switchport to send data out of
* A switch receives traffic with itself as the dst MAC, and the sender as the src MAC. It sends that traffic with its own interface as the src, and the next hop as the dst.
* There are also broadcast frames which usually go out to all ports except the originating port
* (describe a switching loop)
* Protocols like Spanning Tree Protocol (STP) can prevent switching loops. You're unlikely to see these on Linux, though the bridge can run STP

### L2/L3 - ARP

* Whilst ARP is a Layer 2 protocol, it carries data used to make Layer 3 traffic decisions, it is a "glue" protocol between L2 and L3
* A system will ARP out with a request Who has 192.168.0.1? Tell me at 192.168.0.100 with the broadcast MAC as the dst, and its MAC as the source
* The system with the target IP will reply 192.168.0.1 is at <my mac address> with the previous sender's MAC as the dst, and its MAC as the source
* A system can also send an ARP request or ARP reply to the broadcast MAC announcing its IP, called a "Gratuitous ARP" or "GARP"
* GARPs are used to perform updates of a system entering the network, or a change on the network, such as a failover of one IP from one MAC to another MAC

### L3 - Network Layer - Routing

* A "routing table" is a list of interfaces and subnets which can be reached over those interfaces
* Given a routing table, a decision can be made to send non-local traffic to the next hop for it to eventually route to its destination
* A more specific route takes precedence over a less specific route. eg: 10.0.0.0/24 is more specific than 10.0.0.0/8
* There is one "default route" per routing table, where all non-matched traffic goes

### L4 - Transport Layer - TCP

* Connection-oriented - both ends must agree to communicate using a common set of parameters
* Guarantees reliability - the data which goes out one end is the same as the data which arrives at the other end
* Flow control - to prevent overwhelming a sender, to handle fluctuations in system capabilities
* Widely used to move data from one place to another over the internet
* We've decided on a host in the layer above, in this layer we decide on the "service" to talk to with a port number
* The interface between the network (kernelspace) and the application (userspace) is called a "socket"

#### Deep dive into TCP


TCP - Transmission Control Protocol

* ''http://www.lovemytool.com/blog/2010/06/practical-tcp-series-the-connection-setup-by-chris-greer.html''
* ''http://www.lovemytool.com/blog/2010/07/practical-tcp-series-tcp-flags-by-chris-greer.html''
* ''http://www.lovemytool.com/blog/2010/07/practical-tcp-series-the-tcp-window-by-chris-greer.html''
* ''http://www.lovemytool.com/blog/2010/08/practical-tcp-series-sequence-and-acknowledgement-numbers-by-chris-greer.html''
''http://www.lovemytool.com/blog/2010/09/practical-tcp-series-connection-teardown-expected-and-unexpected-by-chris-greer.html''


### L4 - Transport Layer - UDP

* Connectionless with no guarantee of reliability - "say and spray"
* Think of TCP without any of the good stuff
* Also has a lower overhead than TCP, so can theoretically go faster than TCP
* Has its uses, where on-time data is preferable to reliability. eg: Streaming video, VoIP
* Generally the larger a datagram, the more likely loss or corruption is
* Max datagram size 64k

### Packet Capturing

tcpdump -s 0 -n -i ethX -w /tmp/$(hostname)-$(date +"%Y-%m-%d-%H-%M-%S").pcap

Walkthru of all options:


`-s 0`
 Capture all of a frame up to 64kb, not just the headers which might not tell us the full story. If you're troubleshooting a REALLY big transfer like nfs, you might experiment with setting this to 512 or 256 to save space.


`-n`
 Don't perform name resolution. I don't think this actually does anything when we're doing a binary capture, but it can't hurt.


`-i ethX`
 The interface to capture on. If we leave this out, it just captures on the first interface it finds, which might be the wrong one. We also need to specify interface if we're troubleshooting issues with multiple interfaces, such as bonding or traffic in one interface and out another.


`-w file`
 The filename to write to.


Capture filtering - man wireshark


Don't filter on host or port unless we really have to. We'd rather see all traffic if possible, as we may miss something important. We can filter out irrelevant traffic in wireshark or tshark afterwards.


#### Rolling capture

A large capture (say more than 1Gb) can be difficult to upload, and difficult to open at our end because Wireshark needs a lot of memory to open captures (about 4x the size of the capture).

We'd much prefer a smaller file size, say about 250Mb.

tcpdump -C 250 -W 10 -s 0 -n -i ethX -w /tmp/$(hostname)-$(date +"%Y-%m-%d-%H-%M-%S").pcap


-C 250 Capture into 250Mb files.


-W 10 Capture 10 files numbered 01 to 10, then overwrite 01 with 11, overwrite 02 with 12, and so on.


### Packet Analyzing

* Find the customer timezone from sosreport (cat etc/sysconfig/clock)
 eg: ZONE="US/Eastern"
* Use the customer's timezone like
 TZ="US/Eastern" wireshark capture.pcap

Display filters

* Why filter? To remove irrelevant information and focus in on what's important

What to use in a filter?

* IP address - look in the sosreport's ip addr or ask the customer - filter ip.addr eq 192.168.0.1
* Ports - look in the sosreport's netstat -neopa or ask the customer - filter tcp.port eq 80
* As you learn what you're looking for, you'll be able to filter for specific things easier
* If you can think of what you want to filter for but don't know how, search Google. Someone on StackExchange or Ask Wireshark will have done it before.
* PacketLife Display Filter Cheat Sheet - http://media.packetlife.net/media/library/13/Wireshark_Display_Filters.pdf

#### Wireshark - graphical


Opening capture in Wireshark

* Quick win - Analyze, Expert Info - provides data points which Wireshark considers interesting
Quick win - Statistics, IO graph - provides a graph of throughput which can let you see times when throughput has decreased (or not)

* Building a filter using selections - Right click on an expansion and "Prepare filter"
* Building a filter using filter wizard - Analyze, Display Filters, Expression
Following a TCP stream - filter tcp.stream eq X


#### tshark - commandline


Opening capture in tshark - tshark -n -r filename.pcap "filter"

* Quick win - Expert Info - `-q -z expert
Quick win - -z analysis - -z rpc,srt,100003,3 for NFSv3

* Building a filter
* Selectively displaying columns
* Following a TCP stream
Extracting specific protocol information - -O protocol


### Other tools

* capinfos - use to get the time a capture spans with grep time\:
* editcap - can be used to thin down a packet capture to provide to collaborators, build your filter and reverse it with -r

### Example captures

* A TCP session - tcp-session.pcap
* Retransmissions in CIFS transfer between RHEL and Windows - cifs-mount_and_write.pcap
* A TCP SACK option is included in packets #31, #33, #35, and #37. The missing segment is retransmitted in packet #38. - TCP_SACK.cap
* Looking at protocol data (or not looking at protocol data) - telnet.cap and SSHv2.cap
* Getting a picture from a webpage - HTTP.cap
* An NFS mount - nfsmount.pcap
* Case 01007915 files - strace, netstat, pcap
* more here...

### General Advice

* Get the start of a connection if you can. If we have no handshake we cannot see which options were negotiated, and we may miss the protocol's handshake which can be important.
* Don't rely on the Expert Info. It can be good to look at key points, but one retransmission doesn't indicate a giant problem.
* Once a session is ESTABLISHED there is not really a distiction between client and server, think "sender" and "receiver" instead. Either end can close the connection.

Exercises
---------

### Sample captures

Take the sample captures and explore them. Filter them and try to find the same things I did. Make wrong filters so you don't see what is useful anymore. It might take a few goes to get a filter right and that's perfectly fine.

* Wireshark Sample captures - ''http://wiki.wireshark.org/SampleCaptures''
* PacketLife sample captures - ''http://packetlife.net/captures/''

### Common Protocols

Generate some traffic in common protocols, capture it, and look at what the protocol contains.

* telnet (port 23)
* ssh (port 22)
* FTP (port 21 and 20)
* HTTP (port 80)
* HTTPS (port 443)
* NFS (port 2049 and friends)

Source: http://file.bne.redhat.com/~jbainbri/network-course.html
Friday 04 April 2014
