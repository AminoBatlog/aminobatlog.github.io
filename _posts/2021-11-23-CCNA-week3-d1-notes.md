---
title: CCNA第三周笔记Day1
key: network20211123
pageview: true
comment: true
tags:
  CCNA
  Network
---

今天上了动态路由协议,重点强调了OSPF

奇怪了我自己看CCNA书的时候EIGRP和OSPF还有RIP啥的都有讲,这次居然只讲OSPF,可能na阶段只有OSPF吧

不懂了反正看就完了

<!--more-->

---

> Day 1 

**Category of Dynamic Routing Protocol**:

Dynamic Routing Protocol:

​		External Gateway Protocol -> BGP

​		Internal Gateway Protocol:

​				Link State Protocol -> OSPF, IS-IS

​				Distance Vector Protocol -> RIPv2, EIGRP

**Administrative Distance**:

AD is for priority, Metrix for best path

AD first, then Metrix

![Alt Text](/assets/images/2021-11-23/1.png "AD comparison")

**Convergence**:

When all routers contain same network information

---

OSPF settings:

```
Router(config)# router ospf <process-id>
```

The process id is no necessary that have to be the same for each router. The process id is only for the current router

```
Router(config-router)# network <address> <wildcard-mask> area <area-id>
```

This command is to announce the network to OSPF area

Frequent use checking commands:

```
// check OSPF neighbor table
Router# show ip ospf neighbor
// check routing table
Router# show ip route
// clear routing table
Router# clear ip route *
// display OSPF status in console
Router# debug ip ospf
```

---

内容说多不多说少不少(毕竟课本里好几页在课上就总结了几点)

有空去看看课本补充补充好了x顺便为NP做准备~