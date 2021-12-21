---
title: 区块链笔记 状态树
key: blockchain20211220
tags:
  Blockchain
  ETH
---

区块链笔记5:

状态树

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

> 状态树

用什么样的数据结构来实现account based ledger呢

**addr -> state**

ETH中账户地址是160位(20B), 一般表示为40个16进制的数

状态, 就是上次提到的外部账户的balance和Nonce, 对于合约账户还另外有code storage

那么要用什么样的数据结构实现这个映射呢

无法使用BTC用的方法构建, 因为BTC构建的是新的交易, 而ETH要构建所有的账户, 数量不是一个量级的

如果直接把账户放入merkle tree中的话, 因为Merkle Tree没有提供高效的查找修改的方法, 麻烦

而且如果不用Sorted Merkle Tree的话, 构建出来的Merkle Tree是不唯一的, 导致根哈希值不一样. 

在BTC里, 获得的交易顺序每个节点也是不一样的, 但是最终是有记账权的节点决定的. 但ETH没法这么做, 因为ETH要这么做的话, 得有记账权的节点发布包含所有账户状态的数据到区块链中, 数量太大了, 而实际发生状态变化的账户仅仅只是几个账户

最终, ETH所使用的是一个叫做**MPT**的结构



在此之前, 先看看一个数据结构叫做**trie树, 也叫做字典树**

比如我们现在有5个单词, general, genesis, go, god, good

他们构成trie树的结果是

![img](/assets/images/2021-12-20/trie tree.png)

优点:

在ETH中, 因为地址是40个16进制的数, 所以分叉数目(branching factor)是17, 0-f再加上一个结束标志符

而且trie的查找效率取决于键值的长度, 在上面的trie例子中, 不同单词不一样长, 但是在ETH中, 大家都是40位, 所以所有账户的查找效率一样

trie中只要使用的地址不一样, 就不会发生碰撞

在Merkle Tree中, 插入的顺序不一样, 最后构建的树结构不一样, 但是trie中, 只要输入不变, 无论插入顺序如何, 构成的树是一样的树

在ETH中, 每次只有部分账户的状态更新, 用trie可以保证每次只需要访问部分节点就行了, 不用遍历整棵树

缺点:

trie会出现一脉相承的节点, 比较浪费存储空间



**所以我们是用Patricia Tree/Trie, 进行了路径压缩的trie**

![img](/assets/images/2021-12-20/patricia tree.png)

就会发现高度变低了, 查找变快了

但是如果新插入一个数据, 那压缩的路径可能就需要拓展开来

所以当键值之间的差距比较大的时候, 而且键值比较长的时候, 压缩路径效果比较显著

比如对于比较长的单词, 而且相差比较大比较离散, 但是只是使用trie, 就会变成

![img](/assets/images/2021-12-20/long words trie.png)

如果用patricia tree的话, 就会

![img](/assets/images/2021-12-20/long words patricia tree.png)

所以键值分布比较稀疏的时候, 压缩效果好

而在ETH中, 因为地址是160位, 但是账户并没有这么多, 所以排布是稀疏的. 地址足够长, 分布足够稀疏, 就可以防止账户冲突



**MPT(Merkle Patricia Tree)**

就是把所有的账户构建一个Patricia Tree提高效率, 然后把里面的普通指针, 换成Hash Pointer, 最后计算出的root hash, 写入区块的header里

所以到时候要证明账户余额, 可以自己自下而上形成一个Merkle Proof, 证明

同样可以证明non-membership

Notes: ETH里也有交易树, 不过现在讲的是账户状态的状态树



ETH中使用的是**Modified MPT**:

简化地址之后举个例子

![img](/assets/images/2021-12-20/ETH sample.png)

Notes: 如果出现了路径压缩, 会有Extension Nodes

那么相邻两个区块的状态树有所不同

![img](/assets/images/2021-12-20/MMPT between blocks.png)

在这个例子中, 是一个合约账户发生了改变, 那么只需要记录改变的部分就好了

那为什么要记录之前的状态, 而不能直接改变呢?

因为ETH中, 出块时间变成了10几秒, 所以出现分叉是常态, 那么orphan block需要进行rollback, 回到分叉点的block, 然后再顺着正确的链继续走

BTC因为记录的是交易, 所以可以很简单的直接回滚, 但是ETH不行, 因为ETH有智能合约, 功能比较复杂, 不方便简单的直接回滚, 所以需要记录之前的状态, 从而可以直接读取前面的状态进行回滚, 而不是像BTC一样进行简单的推算就可以解决的.

ETH的 block header定义域

![img](/assets/images/2021-12-20/ETH block header.png)

Notes: root是状态树的root hash, TxHash是交易树的, ReceiptHash是收据树, Bloom跟收据树相关, 可以提供高效查询

ETH的区块定义域

![img](/assets/images/2021-12-20/ETH block.png)

真正发布出去的内容

![img](/assets/images/2021-12-20/ETH block publish.png)



在ETH中, 键值对都是经过RLP(Recursive Length Prefix)序列化过后存储的, RLP只支持nested array of btyes