---
title: 区块链笔记 以太坊概述 账户
key: blockchain20211219
tags:
  Blockchain
  ETH
---

区块链笔记4:

以太坊概述 账户

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

> Introduction

BTC被称为区块链1.0, ETH被称为区块链2.0

ETH针对BTC进行了一些改进, 比如

**出块时间**:

BTC出块10分钟太慢了, ETH的出块时间大幅度降低到10几秒, 为了适应这个时间, ETH还发明了一套基于ghost的共识机制

**mining puzzle**:

BTC是计算密集性的, 比拼计算算力, 导致挖矿设备的专业化, 有人认为和去中心化的概念不符合. 而ETH是memory hard mining puzzle, 和内存有关, 在这个一定程度上限制了ASIC芯片的使用, 被称为ASIC resistance

**证明**:

ETH用权益证明代替工作量证明, 就是用 proof of stake 代替 proof of work

**smart contract**:

ETH新增了对智能合约的支持, 同时BTC只是货币去中心化, 而ETH新增了去中心化合约

智能合约是通过技术手段, 取代司法手段

去中心化的货币的好处:

适合跨国转账, 和跨国合同, 各个国家的司法手段都不一样, 但是可以用去中心化的智能合约统一

---

> 账户

BTC是transaction based ledger, 要知道钱包里有多少钱, 需要根据UTXO进行推算

按照正常的思路, 只有存钱的时候才会追溯来源, 花钱的时候是不需要的

另一件事情是BTC一次交易获得的BTC, 需要一次性花出去, 不能只花一部分

而ETH采用的是account based ledger

**优点**:

所以在花钱的时候, 无需说明来源, 转账的时候也可以随心所欲的转

而且基于账户的模式, 可以防止double spending attack

**缺点**:

虽然没有double spending attack, 但是有**replay attack**

当A给B转账之后, 写入区块链, 但是B此时又把这个交易广播了一次, 那其他节点以为又进行了一次转账, 就又记录了一次

解决方法:

在其中加入一个计数器Nonce, 记录这个账户到目前为止发布了多少次交易, 并且作为参数传入到Tx里, 然后整个内容被付款方签名

当一个交易产生的时候, 全节点会记录里面的这个计数器, 并更新到自己的数据库里, 如果有人重放同样的交易, 里面的Nonce会出现重复, 节点们会不认可这个交易

<u>Notes: 这里的Nonce不是BTC里的Nonce, 不是随机数, 而是一个计数器</u>

ETH有两类账户

**外部账户externally owned account**:

有点类似于BTC的账户, 公私钥控制的, 也叫做普通账户

该账户的状态有, 余额balance, 和计数器Nonce

**合约账户smart contract account**:

同样有balance Nonce, 但是这里的Nonce不是记录交易次数, 而是记录调用其他账户的次数, 合约账户不能主动发起交易

所有的交易只能由外部账户发起, 但是可以外部账户发布交易调用的一个和合约账户, 合约账户调用合约

合约账户除了上面提到的两个, 还有代码code, 和存储storage

那么合约账户怎么被调用呢, 当合约创立的时候, 会有一个合约地址, 那么合约账户就可以调用这个合约地址, 调用之后代码是不变的, 但是存储会变



ETH模型的目的:

BTC隐私保护不错, 可以买完换个账户, 但是ETH要支持智能合约, 要求参与者要比较稳定的身份

所以有人提出用ETH做一些金融延伸品(financial derivative)