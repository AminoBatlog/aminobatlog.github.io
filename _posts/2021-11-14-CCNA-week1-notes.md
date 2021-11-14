---
title: CCNA第一周笔记汇总
key: network20211114
preview: true
comment: true
tags:
  CCNA
  Network
---

上了一周CCNA课了，还好我在好几年前就已经把这些内容看了好多遍了，虽然没有完全记住，但是至少不会碰到初见杀，那么来简单的做做笔记记录一下好了

---



> Day 1 orientation

Some briefly introduction about the **course and the future jobs** for network students.

At least I got some basic understanding about what I gonna do in the future (as long as I quit moving on to cybersecurity and stick with network engineering only instead)

![Text Alt](/assets/images/2021-11-14/work.png "job list")

Still don't know what about the cyber guys, just wait and see

**OSI model**

**Data type and transmission type**:

![Text Alt](/assets/images/2021-11-14/type.png "types")

Basically we are using Full Duplex, but some TVs use Simplex.

Although what I have are using auto-negotiation

**Network devices:**

![Text Alt](/assets/images/2021-11-14/network devices.png "some network devices")

Hub, Switch, Router. The hub literally nobody using.... probably

---



> Day 2 Physical Media and TCP/IP Protocol Suite

> >**Copper Media:** Basically what we use normally as home users

![Text Alt](/assets/images/2021-11-14/copper media.png "copper media")

For home users, if we got 1000M Internet, we'd better use CAT-5e or CAT-6, the rest above are no necessary.

For the cable sequence standard, we **mostly use 568B in both sides**, cuz we have a technology called **Auto MDI/MDIX**

For PoE, we recommend use 4,5 for positive, and 7,8 for negative

> >**Fiber Media**: Something I am not familiar with

![Text Alt](/assets/images/2021-11-14/fiber media.png "fiber media")

Just remember Single Mode for **long distance transmission** and Multimode for long distance as well, but **relatively shorter than Single Mode**.

Single Mode is normally **yellow**, Multi is normally **orange**

Single Mode use **laser**, Multi use **LED**

Don't have any notes for TCP/IP Protocol Suite, may I already got them?

---

> Day 3-1: VLSM 

![Text Alt](/assets/images/2021-11-14/netmask.png "netmask")

Some basic thing that I need to remember (for exam only)

![Text Alt](/assets/images/2021-11-14/calculation.png "some good calculation hints")

Also a good way to calculate the netmask, available IP addresses, the network IP, and broadcast IP and so on.

> Day 3-2: Cisco Basic Commands

Enter and leave between execution mode and privilege mode

```
Router> enable
Router# disable
Router>
```

Show commands

```
// show interface status
Router# show interface

// show what we have in the flash
Router# show flash

//show startup-config which in the NVRAM
Router# show startup-config

// show operation version 
Router# show version

// show processes and the protocols
Router# show processes CPU
Router# show protocols

// show running config which in the RAM
Router# show running-config

// show what we have in the table and buffer
Router# show memory
Router# show stacks
Router# show buffers

```

Set up hostname

```
Router# configure terminal
Router(config)# hostname AminoRouter
// reset the hostname
AminoRouter(config)# no hostname
```

Password settings

console password: Require you to enter password while you connecting to the console

```
Router(config)# line console 0
Router(config)# password <password>
Router(config)# login
```

Privilege password: Require you to enter password while you entering the privilege mode

```
// not recommand, use secret instead will be safer
Router(config)# enable password <password>
// better way
Router(config)# enable secret <password>
```

VTY password: Require you to enter password while you telneting to the device

```
Router(config)# line vty 0 4
Router(config)# password <password>
Router(config)# login
```

Encrypt passwords: a **weak way** to protect the plaintext password to make sure non-authorize access wouldn't directly check the plaintext password. (I remember could be Rainbow-Table attacked)

```
Router(config)# service password-encryption
```

MOTD: A message that will be displayed for anyone who log in to the device

```
Router(config)# banner motd <message>
```

Config Management: Save, reload, and remove the config

```
Router# copy running-config startup-config
// better way with less letters
Router# wr
// reload without save
Router# reload
// delete config files
Router# erase startup-config
// 2nd way for deleting
Router# erase NVRAM:startup-config
```

Interface Configuration

```
Router(config)# interface <interface-type> <port|slot|subslot>
// IP address set up
Router(config-if)# ip address <ipaddress> <netmask>
// set up clock rate as DCE
Router(config-if)# clock rate <rate>
// add description for interface
Router(config-if)# description <description>
// start the port
Router(config-if)# no shutdown
// administratively shutdown the port
Router(config-if)# shutdown
// show interface status
Router# show interfaces <interface-type> <port|slot|subslot>
// show all interfaces status
Router# show interfaces brief
```

---

第一周的课上完啦~所有的笔记暂时就这样了，也许有后续的笔记再进行补充好了

