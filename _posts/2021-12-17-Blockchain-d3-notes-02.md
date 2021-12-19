---
title: 区块链笔记 匿名性 零知识证明
key: blockchain20211217002
tags:
  Blockchain
  BTC
---

区块链笔记3_2:

匿名性 零知识证明

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

> 匿名性

BTC交易不要求真名, 可以用地址, 但是有人觉得这不是完全的匿名, 是pseudonymity

破坏BTC匿名性的几个因素:

可以人为创建多个账户, 但因为交易是明文的, 所以有可能被推断出来联系到一起去

这个账户有可能和现实生活中的身份联系起来, 资金转入转出可能被追踪, 从而追踪比特币交易, 或者在现实社会中用比特币支付, 会和显示身份联系起来

Hide your identity from whom?

提高匿名性在应用层和网络层入手

多路径转发, coin mixing, 

---

> 零知识证明

零知识证明是证明者向验证者证明一个陈述是正确的, 但无需透露该陈述是正确的之外的任何信息

同态隐藏

![Alt Text](/assets/images/2021-12-17/Homomorphic hiding.png "同态隐藏")

这个性质可以满足零知识证明

盲签

![Alt Text](/assets/images/2021-12-17/blind sig.png "盲签")

零币和零钞

![Alt Text](/assets/images/2021-12-17/zerocoin and zerocash.png "零币零钞")