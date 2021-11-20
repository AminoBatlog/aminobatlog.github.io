---
title: CCNA第二周笔记Day3
key: network20211118
pageview: true
comment: true
tags:
  CCNA
  Network
---

又结束了一周的学习，进行一个笔记的记录

<!--more-->

---

> Day 3 Part 1 STP

**Some basic issues**:

Single Point of Failure: so we need multiple path to maintain the link reliability

Redundancy Topology: solve single point of failure issue, but cause:

1. Broadcast storm: multiple switches keep transmitting data packets to each other and never stop
2. Multiple frame duplicate: multiple switches send the same data packet to end-user
3. MAC unstable: switches keep learning the same MAC address from different port

**Operation of Spanning-Tree Protocol**:

1. choose one root bridge for each broadcast domain
2. choose one root port for each non-root bridge
3. choose one designated port for each part
4. block rest ports

![Alt Text](/assets/images/2021-11-20/1.png "BPDU")

**Specific Operation for each step**:

**Step 1 - choose one root bridge for each broadcast domain**:

The switch who has the lowest bridge ID will be the root bridge:

Bridge ID (8B) = bridge priority (2B) + bridge MAC address (6B)

**Step 2 - choose one root port for each non-root bridge**:

The considered factors:

1. lowest bridge ID
2. lowest path <u>cost</u>
3. lowest <u>sender bridge ID</u>
4. lowest <u>port ID</u>

**Step 3 - choose one designated port for each part**:

1. lowest bridge ID
2. lowest path <u>cost</u>
3. lowest <u>sender bridge ID</u>
4. lowest <u>port ID</u>

Notes: the ports on the root bridge should be the designated ports

**Step 4 - block rest ports**

---

**STP port status**:

Blocking (if loss of BPDU detected) (max 20 sec) >>>>

Listening (if the port is still remains down) (forward delay 15 sec) >>>>

Learning (forward delay 15 sec) >>>

Forwarding

**RSTP-802.1W**: 

802.1D STP could back to connection within 1 min (too long)

802.1W STP could converge rapidly

**RSTP port status**:

Discarding =  Disable, Blocking, Listening

Learning = Learning

Forwarding = Forwarding

**RSTP port roles**:

Root port - forwarding

Designated port - forwarding

Alternate port - blocked - AP is backup for RP, could transfer within 5 sec

Backup port - Blocked - BP is backup for DP, could transfer within 1 sec

**RSTP setting**:

```
Switch(config)# spanning-tree mode rapid-pvst
// choose root/secondary root
Switch(config)# spanning-tree vlan <vlan> root [primary|secondary]
// setup priority
Switch(config)# spanning-tree vlan <vlan> priority <priority>'
// check the settings
Switch# show spanning-tree vlan <vlan>
```

**PortFast**:

Could help the 2nd layer port transfers into forwarding status immediately

```
// enable the port
Switch(config-if)# spanning-tree PortFast
// enable globally
Switch(config-if)# spanning-tree PortFast default
// disable the port
Switch(config-if)# no spanning-tree PortFast
// set port to access and enable PortFast
Switch(config-if)# switchport host
```

---

> Day 3 Part 2 Routing

**Routing Table**:

The routing table is contained in RAM, usually have those information:

1. direct network: when the device is linked to the interface from the other router directly
2. remote network: The network is not directly linked to the other one
3. specific information about network: including source info, network address, netmask, and next hop address

There are three ways to establish routing table:

1. direct: the network that directly linked
2. static: manually configure by administrators
3. dynamic: learning from the dynamic routing protocol

```
// display routing table
Router# show ip route
```

**Static route**:

configure by administrators manually, display begin with **S**

```
Router(config)# ip route <address> <netmask> <next hop address|exit interface>
// configure default gateway
Router(config)# ip route 0.0.0.0 0.0.0.0 <gatewayAddress>
```

