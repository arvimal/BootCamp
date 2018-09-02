<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Network Troubleshooting](#network-troubleshooting)
  - [1. OSI Layer troubleshooting](#1-osi-layer-troubleshooting)
    - [1.1. Layer 1 - Application Layer](#11-layer-1---application-layer)
    - [1.2. Layer 2 - Presentation layer](#12-layer-2---presentation-layer)
    - [1.3. Layer 3 - Session layer](#13-layer-3---session-layer)
    - [1.4. Layer 4 - Transport layer (TCP packet capture using tcpdump/Wireshark)](#14-layer-4---transport-layer-tcp-packet-capture-using-tcpdumpwireshark)
    - [1.5. Layer 5 - Network layer (Use ping and traceroute)](#15-layer-5---network-layer-use-ping-and-traceroute)
    - [1.6. Layer 6 - Data Link layer](#16-layer-6---data-link-layer)
    - [1.7. Layer 7 - Physical layer](#17-layer-7---physical-layer)
  - [2. How do you distinguish a DNS problem from a network problem?](#2-how-do-you-distinguish-a-dns-problem-from-a-network-problem)
  - [Scenarios](#scenarios)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Network Troubleshooting

## 1. OSI Layer troubleshooting

Networks can be troubleshooted in multiple ways. One important method is to work along the OSI layers.

Troubleshooting along the OSI layers (Note that not everything aligns exactly as the OSI layers)

**NOTE:**

* While troubleshooting N/W applications, it's first better to check the Application layer steps and then move to the lower three steps.
* The network connectivity troubleshooting comes in the lower three layers. If that's all fine, we can come up back to the top four layers, and concentrate specifically on the remaining three layers (Presentation, Session, Transport layers)

### 1.1. Layer 1 - Application Layer

    1. Check if the Application is up and running (ps -aux, pgrep, top)
    2. Check if the application is listening on the port. (netstat)
    3. Check if firewall rules are blocking the port (iptables -L and list the port/service)

### 1.2. Layer 2 - Presentation layer

    1. Since this layer deals with mapping various formats to the underlying layers, check if things like encryption etc.. are causing problems.

### 1.3. Layer 3 - Session layer

    1. Check if localhost is able to establish a session to the running service, as well as other nodes.
    2 A successful session would show an ESTABLISHED flag in netstat, provided the service works over network sockets.

### 1.4. Layer 4 - Transport layer (TCP packet capture using tcpdump/Wireshark)

    * This may require packet captures and analysis using the RFC for that particular service.
    * Starting a packet capture and then trying to establish a session from the client to the server would help.
    * Analyze the packet capture and check if the requests and replies are according to the RFC. There are multiple instances where a service implementation breaks an RFC and the client won't proceed further since it doesn't know what to do next. Lots of NFS/CIFS issues which can come up in this layer.

### 1.5. Layer 5 - Network layer (Use ping and traceroute)

**NOTE:**
Ping the network to understand ICMP packets are going through. If so, IP packets should go through as well.

    * Ping the local loopback to understand the network stack works as intended.
    * Ping the localhost IP address
    * Ping other hosts in the N/W subnet.
    * Ping the Gateway
    * Ping multiple hosts outside the subnet, including the destination (if the destination is outside the N/W subnet)

If `ping` does not work, use `traceroute` to understand where it fails. The packets may not be routed properly to the destination, and may have been failing somewhere in between.

The `traceroute` command also can point to a local `Switch` issue, in case the packets are not forwarded by the switch to the next port.

If these doesn't show any problem, we can go back to the Presentation/Session/Transport layers to understand what is happening. Go up..

### 1.6. Layer 6 - Data Link layer

Understand if the Ethernet device is able to send the frames properly.

In many cases, `ethtool` can help to understand what is happening in this layer, as well as the layer below, the Physical layer.

Ethernet not properly detecting the device, or having a duplex mode different can cause problems in this layer.

```bash
# ethtool enp0s25
Settings for enp0s25:
    Supported ports: [ TP ]
    Supported link modes:   10baseT/Half 10baseT/Full
                            100baseT/Half 100baseT/Full
                            1000baseT/Full
    Supported pause frame use: No
    Supports auto-negotiation: Yes
    Advertised link modes:  10baseT/Half 10baseT/Full
                            100baseT/Half 100baseT/Full
                            1000baseT/Full
    Advertised pause frame use: No
    Advertised auto-negotiation: Yes
    Speed: 1000Mb/s
    Duplex: Full
    Port: Twisted Pair
    PHYAD: 1
    Transceiver: internal
    Auto-negotiation: on
    MDI-X: on (auto)
Cannot get wake-on-lan settings: Operation not permitted
    Current message level: 0x00000007 (7)
                   drv probe link
    Link detected: yes
```

### 1.7. Layer 7 - Physical layer

## 2. How do you distinguish a DNS problem from a network problem?

---

## Scenarios

1. A webservice is very slow(multi-tier application, which has app, db..etc in different servers)

   * How will you troubleshoot?
     ( Lot of sub questions for this)

2. A sysadmin is operating a linux system with 6 network cards, being that only eth0 is plugged in and working. Sysadmin does a few commands:

```bash
# ifconfig eth0
eth0      Link encap:Ethernet  HWaddr 00:11:44:43:63:E1
          inet addr:192.168.1.104  Bcast:192.168.1.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2231 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2522 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:100
          RX bytes:780859 (762.5 KiB)  TX bytes:373261 (364.5 KiB)
          Base address:0x8000 Memory:c0220000-c0240000

# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
0.0.0.0         192.168.1.1     0.0.0.0         UG    0      0        0 eth0

# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

Questions:

* What is the IP address of this system and the router it is configured to use?
* How can one know the speed of the interface eth0?
* How can one know the pci card that is currently eth0 and the driver it's currently using?
* Sysadmin left a tcpdump running as follows:

```bash
# tcpdump -n -nn -i eth0
```

on one terminal, while issuing commands on another terminal. With the following output from tcpdump, can you tell what commands were run, and if they  succeeded or failed?

```bash
13:43:35.087797 IP 192.168.1.104 192.168.1.1: ICMP echo request, id 10254, seq 1, length 64
13:43:35.089529 IP 192.168.1.1 192.168.1.104: ICMP echo reply, id 10254, seq 1, length 64
13:43:36.087483 IP 192.168.1.104 192.168.1.1: ICMP echo request, id 10254, seq 2, length 64
13:43:36.088225 IP 192.168.1.1 192.168.1.104: ICMP echo reply, id 10254, seq 2, length 64
14:04:09.015376 IP 192.168.1.104.51276 192.168.1.1.33434: UDP, length 40
14:04:09.015759 IP 192.168.1.104.33382 192.168.1.1.33435: UDP, length 40
14:04:09.015820 IP 192.168.1.104.45147 192.168.1.1.33436: UDP, length 40
14:04:09.015877 IP 192.168.1.104.33561 192.168.1.1.33437: UDP, length 40
14:04:09.015974 IP 192.168.1.104.42414 192.168.1.1.33438: UDP, length 40
14:04:09.016034 IP 192.168.1.104.56430 192.168.1.1.33439: UDP, length 40
14:04:09.016091 IP 192.168.1.104.56784 192.168.1.1.33440: UDP, length 40
14:04:09.016147 IP 192.168.1.104.59999 192.168.1.1.33441: UDP, length 40
14:04:09.016207 IP 192.168.1.1 192.168.1.104: ICMP 192.168.1.1 udp port 33434 unreachable, length 76
14:04:09.016281 IP 192.168.1.104.35841 192.168.1.1.33442: UDP, length 40
14:04:09.016953 IP 192.168.1.1 192.168.1.104: ICMP 192.168.1.1 udp port 33435 unreachable, length 76
14:04:09.017452 IP 192.168.1.1 192.168.1.104: ICMP 192.168.1.1 udp port 33436 unreachable, length 76
14:04:09.017952 IP 192.168.1.1 192.168.1.104: ICMP 192.168.1.1 udp port 33437 unreachable, length 76
14:04:09.018452 IP 192.168.1.1 192.168.1.104: ICMP 192.168.1.1 udp port 33438 unreachable, length 76
14:04:09.019201 IP 192.168.1.1 192.168.1.104: ICMP 192.168.1.1 udp port 33439 unreachable, length 76
14:51:43.018084 IP 192.168.1.104.42034 192.168.1.1.23: S 2650503081:2650503081(0) win 5840 <mss 1460,sackOK,timestamp 5378506 0,nop,wscale 7>
14:51:43.018890 IP 192.168.1.1.23 192.168.1.104.42034: R 0:0(0) ack 2650503082 win 0
14:51:48.017352 arp who-has 192.168.1.1 tell 192.168.1.104
14:51:48.017600 arp reply 192.168.1.1 is-at 00:1e:35:3e:1c:41
14:51:55.249807 IP 192.168.1.104.33796 192.168.1.1.80: S 2660968684:2660968684(0) win 5840 <mss 1460,sackOK,timestamp 5390740 0,nop,wscale 7>
14:51:55.251545 IP 192.168.1.1.80 192.168.1.104.33796: S 2605137501:2605137501(0) ack 2660968685 win 5792 <mss 1460,sackOK,timestamp 574003227 5390740,nop,wscale 7>
14:51:55.251580 IP 192.168.1.104.33796 192.168.1.1.80: . ack 1 win 46 <nop,nop,timestamp 5390742 574003227>
14:51:55.251983 IP 192.168.1.104.33796 192.168.1.1.80: P 1:174(173) ack 1 win 46 <nop,nop,timestamp 5390742 574003227>
14:51:55.252543 IP 192.168.1.1.80 192.168.1.104.33796: . ack 174 win 46 <nop,nop,timestamp 574003228 5390742>
14:51:55.289779 IP 192.168.1.1.80 192.168.1.104.33796: P 1:344(343) ack 174 win 46 <nop,nop,timestamp 574003265 5390742>
14:51:55.289839 IP 192.168.1.104.33796 192.168.1.1.80: . ack 344 win 54 <nop,nop,timestamp 5390780 574003265>
14:51:55.291768 IP 192.168.1.1.80 192.168.1.104.33796: F 344:344(0) ack 174 win 46 <nop,nop,timestamp 574003267 5390780>
14:51:55.309637 IP 192.168.1.104.33796 192.168.1.1.80: F 174:174(0) ack 345 win 54 <nop,nop,timestamp 5390800 574003267>
14:51:55.310262 IP 192.168.1.1.80 192.168.1.104.33796: . ack 175 win 46 <nop,nop,timestamp 574003286 5390800>
```

* Still with the tcpdump running, sysadmin runs the command:

```bash
# ping 192.168.1.104 -c3
PING 192.168.1.104 (192.168.1.104) 56(84) bytes of data.
64 bytes from 192.168.1.104: icmp_seq=1 ttl=64 time=0.082 ms
64 bytes from 192.168.1.104: icmp_seq=2 ttl=64 time=0.064 ms
64 bytes from 192.168.1.104: icmp_seq=3 ttl=64 time=0.057 ms

--- 192.168.1.104 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.057/0.067/0.082/0.014 ms
```

However the tcpdump does not capture any of the traffic. The sysadmin runs the command again in flood mode, and still nothing is captured. How can one tell if this is a problem or not, and what files or commands could be able to prove the previous statement?
