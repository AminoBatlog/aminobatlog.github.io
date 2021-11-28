---
title: CCNA第三周笔记Day2,3
key: network20211128
pageview: true
comment: true
tags:
  CCNA
  Network
---

简单做做笔记,要开学啦

真刺激

<!--more-->

---

> ACL

**Standard ACL**:

Only filter source IP address

**Extended ACL**:

Can filter specific source, destination, port

**The configuration of Standard ACL**:

```
Router(config)# access-list <list-number> [permit|deny] <sourceip> <wildmask>
Router(config)# interface <interface>
Router(config-if)# ip access-group <list-number> [in|out]
```

**The configuration of Extended ACL**

```
Router(config)# access-list <list-number> [permit|deny] <protocol> <sourceip> <source wildmask> <operator port> <desip> <des wildmask> <operator port>
```

P.S.: the "operator port" is like "eq 80"

**The configuration of Named ACL**

```
Router(config)# ip access-list [standard|extended] <name>
Router(config-[std- | ext-] nacl)# [permit|deny] {conditions}
```

P.S.: the condition is almost the same as previous settings

```
// checking access-lists settings
Router# show access-lists
```

---

> NAT

**Terminology**:

Inside Local: the source IP before been NATTED

Outside Local: the destination IP before been NATTED

Inside Global: the source IP after been NATTED

Outside Global: the destination IP after been NATTED

**Static NAT**:

Each inside local address has its own inside global address, the back side is it will waste IP addresses, not really take good use of IPs

Static NAT configuration:

```
Router(config)# ip nat inside source static <local-ip> <global-ip>
Router(config-if)# ip nat [inside|outside]
// check NAT table
Router# show ip nat translations
```

**Dynamic NAT**:

Each inside local address has inside global address, but instead of static relationship, the dynamic NAT will pick one address from the inside global address pool. After the data transmission finished, the inside global address will return back to the pool, then it could be used by other hosts.

Dynamic NAT configuration

```
Router(config)# ip nat pool <poolname> <start-ip> <end-ip> netmask <netmask>
// Create a ACL for choose network to be NAT
Router(config)# access-list <list-number> permit <ip> <wildcard>
Router(config)# ip nat inside source list <list-number> pool <poolname>
```

Port Address Translation, PAT:

Dynamic NAT but using ports. different services from different hosts could use only one or few public IP for data transmission, but using different ports.

```
Router(config)# access-list <list-number> permit <ip> <wildcard>
// create PAT
Router(config)# ip nat inside list <list-number> interface <interface> overload
```

P.S.: using "overload" to enable PAT

---

好耶~