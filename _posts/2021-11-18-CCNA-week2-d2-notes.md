---
title: CCNA第二周笔记Day2
key: network20211118
pageview: true
comment: true
tags:
  CCNA
  Network
---

每次上课做个笔记总是好的，上完课及时回顾一下

今天是上了EtherChannel有关的冗余方案还有DHCP，DHCP挺熟悉的，毕竟Network Server不久前刚用过，虽然人家是在CentOS和Winserver的基础上做的，这个是基于Cisco IOS，应该算是UNIX？

<!--more-->

---

> Day 2 Redundancy technique and DHCP

**EtherChannel:**

1. Combine links which have similar properties
2. Load balance
3. Show as a single logical port

```
// select the ports that you wanna combine
Switch(config)# interface range <port-port>
// shutdown the interface first cuz sometimes they just bug out(for VM environment)
Switch(config-if-range)# shutdown
// enable the channel group
Switch(config-if-range)# channel-group <groupNumber> mode [active|passive|desirable|auto|on]
Switch(config-if-range)# no shutdown
// check Etherchannel settings
Switch# show etherchannel summary
```

**EtherChannel Types:**

<u>PAgP</u>: Cisco protocol

desirable: enable PAgP unconditionally, which act as the active mode

auto: enable PAgP only if a PAgP device is detected, which act as the passive mode

<u>LACP</u>: universal protocol

active: enable LACP unconditionally

passive: enable LACP only if a LACP device is detected

<u>on</u>: no protocol used

Static one, only enable the EtherChannel, but the port which using it need to be the Layer 3 port(no switchport)

**The condition about EtherChannel successfully up**:

1. We already had the corresponding VLAN in our VLAN database
2. The interface is already up
3. the interface is already been allocated into the corresponding VLAN(switchport access vlan)

---

> VRRP (Virtual Router Redundancy Protocol)

**VRRP working principle**:

1. Both routers use VRRP to discover each other
2. Generate virtual router
3. compare priorities and IP addresses
4. One becomes main router, the other becomes backup
5. The default router for hosts points to virtual router
6. When the main router fucked up, automatically switch to the backup(IP address remain the same but the MAC will change)

Notes: the preempt is active by default

Notes_1: **preempt** is: when we have a higher priority or higher IP address router, it will automatically become main router. If the preempt is disabled, only if the active router totally fucked up, the backup one will become the main one.

**VRRP track**:

Monitor some ports, and automatically change the priorities.

When some routers have bad ports, their priority will be deduced.

```
Router(config-if)# vrrp <groupNumber> ip <virtualRouterIP>
// the range of priority is 1-254, and set to 100 by default
Router(config-if)# vrrp <groupNumber> priority <priorityNumber>
// start preempt
Router(config-if)# vrrp <groupNumber> preempt
Router(config-if)# vrrp <groupNumber> track <portNumber>
```

---

> DHCP

**DHCP working principle**:

1. Client sends DISCOVER as broadcast
2. Server sends OFFER as broadcast
3. Client replies REQUEST as broadcast
4. Server replies ACK as broadcast

**DHCP setting**:

```
// start DHCP (cisco enable it by default)
Router(config)# service dhcp
// define DHCP pool
Router(config)# ip dhcp pool <poolName>
// define relative network part
Router(config-dhcp)# network <address> <netmask>
// define default-gateway which will be allocated to users
Router(config-dhcp)# default-router <gatewayAddress>
// set exclusive address(back to the config mode)
Router(config)# ip dhcp excluded-address <address>
// or exclusive a set of addresses
Router(config)# ip dhcp excluded-address <startAdd> <endAdd>
```

**Client setting**:

```
Client(config-if)# ip address dhcp
```

**DHCP relay**:

When client and server are not in the same broadcast domain, the relay will forward the DHCP requests by changing it from broadcast message to unicast

The DHCP relay is configured on the port which is the first port that block the broadcast message (e.g. gateway)

**DHCP relay setting**:

```
Router(config-if)# ip helper-address <DHCPaddress>
```

---

今天没啥图，写起来挺轻松的（至少不用花时间去弄那个图片格式）

下节课从周日变周六了，那么应该周六晚上会进行一个笔记的记(周末就是下午晚上两节课了)

希望能在下学期开学前顺利读完CCNA、CCNP考个证，等毕业了延迟半学期考CCIE然后再读研

完美，刚好可以把gap的半个学期补回来