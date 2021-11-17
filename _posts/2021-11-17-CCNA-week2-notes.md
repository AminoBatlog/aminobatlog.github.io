---
title: CCNA第二周笔记Day1
key: network 20211117
pageview: true
comment: true
tag:
  CCNA
  Network
---

继续进行一个笔记的记

今天是上了关于VLAN和VTP有关的一些知识点

<!--more-->

---

> Day 1 VLAN

**Collision Domain & Broadcast Domain:**

For switch, every interface has its own collision domain, every interface belongs to one broadcast domain

**Function of Routers**:

1. Broadcast control
2. Multicast control
3. Choose the best path
4. Flow control
5. Provide logical address
6. WAN access

**Campus Network Structure:**

Export Layer(OR):

1. WAN access
2. Export strategy
3. Bandwidth control

Core Layer(CO):

1. High-speed switching
2. Server Access
3. Routing

Convergence layer(GS):

1. Bandwidth convergence
2. Link redundancy
3. Devices redundancy
4. Routing

Access Layer(AS):

1. User access
2. Access security
3. Access control

**Function of Switches**:

1. Address(MAC) learning
2. switching and filter frames
3. loop avoidance

**MAC address:**

1. 48 bits
2. IEEE manages and distributes them
3. first 24 bits represent providers, the rest is assigned by providers themselves

**Characteristic of VLAN:**

1. A single VLAN belongs to one broadcast domain, broadcast could not be transmission across VLAN
2. A single VLAN is a logical subnet, different VLAN communicate with each other by using routers
3. VLAN members based on switch port number, allocate VLAN is allocate switch port
4. VLAN working on 2nd Layer in OSI model

**VLAN member mode:**

1. Static VLAN - configure manually
2. Dynamic VLAN - use VMPS to allocate VLAN to host which has particular MAC address
3. Voice VLAN - configure port to voice mode could support IP dialing

**VLAN setting:**

Create VLAN information

```
Switch(config)# vlan <number|number,number|number-number>
Switch(config)# name <name>
```

Specify port to dedicated VLAN

```
Switch(config)# interface <port>
Switch(config)# switchport mode access
Switch(config)# switchport access <vlan>
```

**VLAN range:**

![Text Alt](/assets/images/2021-11-17/1.png "VLAN range reference")

---

**802.1Q:**

![Text Alt](/assets/images/2021-11-17/2.png "802.1Q frame structure")

**Trunk basic setting:**

```
Switch(config)# interface <port>
Switch(config-if)# switchport trunk encapsulation [isl|dot1q]
Switch(config-if)# switchport mode trunk
```

**VTP:**

1. A system that could broadcast VLAN setting
2. According to a management domain, maintain VLAN settings
3. VTP is only sending VLAN setting information on main port
4. Support mixed media connection (FastEthernet, FDDI, ATM)

**VTP mode:**

Server:

1. Create, modify, and delete VLAN
2. send, and repost information announcement
3. Sync
4. Store in NVRAM

Transparent:

1. Create, modify, and delete VLAN
2. repost information announcement
3. Async
4. Store in NVRAM

Client

1. send, and repost information announcement
2. Do not store in NVRAM

Notes: Transparent is only act as a agent, delivery VLAN information. Every single changes in Transparent node will not effect anything to Server and Client

**VTP works:**

1. VTP protocol send VTP announcement via multicast address 01-00-0C-CC-CC-CC'
2. VTP Server and Client sync database based on the highest Revision Version
3. VTP protocol send VTP announcement every 5 min or when some changes have been made

**VTP settings:**

```
Switch(config)# vtp mode [server|client|transparent]
Switch(config)# vtp domain <domain-name>
Switch(config)# vtp password <password>
Switch(config)# vtp pruning
```

Notes: VTP is server mode by default, VTP domain and password are case sensitive, VTP pruning is disable by default.

**VLAN routing:**

Method 1: use multiple physical interface

![Text Alt](/assets/images/2021-11-17/3.png "Physical interface act as default gateway for each VLAN")

Method 2: use One-arm routing

![Text Alt](/assets/images/2021-11-17/4.png "One-arm routing")

```
Router(config)# interface <port>
Router(config-if)# no shutdown
Router(config)# interface <port>.<subport>
Router(config-subif)# encapsulation dot1q <vlan>
Router(config-subif)# ip address <address> <netmask>
ROuter(config-subif)# no shutdown
```

**SVI:**

1. Host management interface, administrators could use this interface to manage switches
2. Gateway interface, used for cross-VLAN communication by 3-layer switches. Could use create SVI, and configure with IP address to achieve routing function

