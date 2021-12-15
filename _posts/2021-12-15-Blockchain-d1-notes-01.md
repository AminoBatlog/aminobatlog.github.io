---
title: 区块链笔记 协议
key: blockchain20211215001
pageview: true
comment: true
tags:
  Blockchain
---

区块链笔记1_1:

协议

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

如果中国银行发行数字货币,仅仅只是拥有数字签名来证明这个数字货币合法,那么可以很轻易的进行复制,导致一份货币可以被花好多次,被称为double spending attack

如果发行数字货币的同时对数字货币进行编号,并且存入数据库中,每次交易之前访问数据库进行验证. 这种方法是可行的,但是是个中心化的解决方案

去中心化方案解决的问题有2:

1. 谁发行数字货币
2. 怎么验证交易有效性

关于验证交易有效性:

每一笔交易都记录进区块链内,然后每一次交易都进行验证,溯源,如果有出现double spending,那么交易不合法,不记录进区块链内

![Alt Text](assets/images/2021-12-15/transaction process.png "交易有效性验证")

交易需要包含几个信息:

购买方的公钥,币的来源,接收方公钥的哈希

如何验证购买方的公钥是正确的公钥而不是第三人拿自己的公钥伪造宣称是购买方的公钥?

在币的来源前面的区块里,在最开始的coinbase里,已经包含了接收方的公钥的哈希,在溯源的时候可以和前面接收方公钥的哈希进行比对确认该购买者就是前面接受这些币的接受者

具体验证过程:

将用户的购买时候的输出脚本,与前方接受货币时候的输入脚本合在一起变成一段程序,如果可以正常执行,则没有问题

**区块信息**:

Block Header:

比特币版本version

指向前一个区块链的指针Hash of previous block header

整个Merkle Tree的根哈希值 Merkle root hash

挖矿的难度目标阈值 Target

随机数 Nonce

Block Body:

交易列表 Transaction List



取hash是对整个header取哈希值

轻节点仅仅保留了header部分,没有参与区块链的构造和维护,只是利用了区块链的信息做一些查询

如何保证记账能同步到所有区块

记录的交易需要达到分布式的共识(Distributed Consensus),大家共同维护一个全局哈希表(Distributed Hash Table)

但是根据FLP impossibility result, 在一个Async系统里,只要有一个人是faulty的,那么就无法达成共识

还有CAP Theorem(Consistency, Availability, Partition Tolerance), 任何一个分布式系统,在这三个性质中,最多只能满足两个

Paxos协议: 能保持一致性,客观存在一直无法达成共识



比特币的机制: 根据算力进行投票

哪个节点计算出了这个区块H(block header)<=target,哪个节点计算出了这个nonce,哪个节点就获得了记账权,只有这个节点才有权利发布这个区块

如果获得记账权的节点发布了一个合法的区块,但是那个区块是在原本的区块链的中间,形成了一个分叉,那么不会被记录,因为不是最长合法链

被抛弃的block成为orphan block

![Alt Text](assets/images/2021-12-15/longest valid chain.png "最长合法链")

这种叫做分叉攻击(Forking Attack),通过插入区块来回滚已经发生的交易. 如果有两个节点同时获得记账权,会获得两个等长的链

那么就会进行竞争,能算出下一个block的节点获得记账权,那么它所承认的chain会成为valid chain,另外一部分的节点所研究出来的block会成为孤儿区块被抛弃

![Alt Text](assets/images/2021-12-15/orphan block.png "孤儿区块的产生")

每个区块拥有block reward机制,存在coinbase transaction,即可以凭空获得BTC,产生新的BTC



女巫攻击: 如果一个节点创建大量的账号超过了半数,那么就获得了绝对的权利

但是因为BTC是根据算力进行投票的,所以一台主机创建多个账号,但是同一台主机的hash rate是不变的,并不能够获得更多的投票权