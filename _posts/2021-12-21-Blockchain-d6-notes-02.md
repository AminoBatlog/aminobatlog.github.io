---
title: 区块链笔记 难度调整
key: blockchain20211221002
tags:
  Blockchain
  ETH
---

区块链笔记6_2:

难度调整

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

BTC是16个区块调整难度, ETH是每个区块都有可能调整挖矿难度

ETH难度调整公式

![img](/assets/images/2021-12-21/eth difficulty.png)

最后的埃普西隆是难度炸弹部分

![img](/assets/images/2021-12-21/diff changed part 1.png)

调整力度x 是父区块除以2048, 作为调整难度的单位

![img](/assets/images/2021-12-21/diff changed part 2.png)

基础部分除外, 最后一个参数是难度炸弹

![img](/assets/images/2021-12-21/difficulty bomb.png)

一开始是没有H' 的操作的, 本来是打算当难度炸弹爆炸的时候, ETH从PoW转换为PoS, 结果因为PoS的共识协议还没做完, 但是难度炸弹的威力已经显现出来了, 就只能更新出了H', 将所有的区块区块好倒退300w进行计算

![img](/assets/images/2021-12-21/difficulty bomb demostration.png)



ETH发展的四个阶段

![img](/assets/images/2021-12-21/state of ETH.png)

难度调整的具体的代码实现

![img](/assets/images/2021-12-21/code implementation.png)

基础部分的计算

![img](/assets/images/2021-12-21/basic calculation.png)

难度炸弹的计算

![img](/assets/images/2021-12-21/diff bomb calculation.png)



ETH的实际统计情况

![img](/assets/images/2021-12-21/eth diff change sample.png)

![img](/assets/images/2021-12-21/block time sample.png)

区块难度情况

![img](/assets/images/2021-12-21/difficulty sample.png)

difficulty是当前区块的难度, total difficulty是整条链的总难度