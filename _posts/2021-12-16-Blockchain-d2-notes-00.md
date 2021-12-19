---
title: 区块链笔记 实现
key: blockchain20211216
pageview: true
comment: true
tags:
  Blockchain
  BTC
---

区块链笔记2:

实现

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

> 概念

比特币采用的是**基于交易的账本模式 transaction-based ledger**

而以太坊是**基于账户的模式 account-based ledger**

查询余额的时候需要根据现有的交易推算账户余额

全节点需要维护UTXO(Unspent Transaction Output):还没有花出去的交易的输出组成的集合

Notes: 一个交易可能有多个输出(一个人给多个人转账了)

UTXO可以查询double spending

每个交易的所有金额的输入要等于所有金额的输出total inputs == total outputs

但是有可能total input要略微大于total output,差值是用作交易费,提供给获得记账权的那个节点

所以获得记账权的节点不仅可以获得coinbase凭空生成的BTC,还能获得在它那里打包的交易的手续费(Transaction fee)

![Alt Text](/assets/images/2021-12-16/btc block.png "比特币区块例子")

在图中右侧有三个hash,第一个为当前block header的hash,第二个为前一个block header的hash,第三个为merkle root的hash,前两个hash都是一长串0开头的,就是挖矿的时候设定的target值

![Alt Text](/assets/images/2021-12-16/btc header.png "比特币区块头的数据结构")

如图示,Nonce是一个32位的无符号整型,就目前比特币而言,有可能遍历完了这2^32种可能,依旧找不到符合条件的Nonce,那么还有什么方法可以调整的嘛?

![Alt Text](/assets/images/2021-12-16/btc header description.png "比特币区块头的文档介绍")

在这个图里,除了Nonce之外,还能更改的只能是Merkle root了,但是这个Merkle root为啥能进行更改呢?因为每挖出一个块,都会得到出块奖励,而对于出块奖励来说,是没有input的,因为是凭空产生的,所以对应的coinbase部分,可以随意写入任何内容,从而改变这个交易对应的hash,改变Merkle root,最终配合Nonce达到更改hash的目的

![Alt Text](/assets/images/2021-12-16/coinbase transaction.png "出块奖励Tx")

那么在普通的交易中,是有input和output的

![Alt Text](/assets/images/2021-12-16/normal transaction.png "普通的Tx")

在图的上方,坐标显示的Output,对于目前的交易来说是Input,之所以显示Output,是因为这个标明了交易的时候所用的BTC的来源,是来源于哪个Tx的Output

在下方有两个Scripts,之前提到将输入和输出Script放在一起执行,如果没出错就代表币合法,但是输入输出script并不是同一个交易的输入输出配对. 而是当前交易的输入,和之前获得币的那个交易的输出合在一起执行, 如果没有问题, 那就合法

> 挖矿过程

每次尝试Nonce的时候,可以看做是一个Bernoulli Trial

Bernoulli Trial: a random experiment with binary outcome

我们进行多个Bernoulli Trial,这些构成了一个Bernoulli process

Bernoulli Process: a sequence of independent Bernoulli trials

B Process的一个性质是无记忆性(Memoryless), 做多个实验, 前面的实验结果对后面的实验结果是没有影响的

对于挖矿来说, 每次找到Nonce的成功率很小, 需要进行大量的实验才能找到, 那么这时候的Bernoulli Process可以用Poisson Process来描述

我们关心的是出块的时间, 那么可以推导出这个出块的时间是服从指数分布的(Exponential Distribution)

![Alt Text](/assets/images/2021-12-16/exp distribution.png "出块难度呈指数形式")

比特币通过定期调整难度,每个区块大概10分钟出一次

这个曲线的特点在于,exp distribution也是memoryless的, 就代表如果大家10分钟没有挖出有效区块,那么继续挖出区块的曲线依旧是这个exp distribution, 那么挖出来区块的期望还是在10分钟, 所以把这种概率称之为progress free

如果这个挖矿的概率不遵从progress free, 会导致做的努力越多的,越容易挖到矿, 那么那些算力高的矿工会有不成比例的优势, 因为算力高, 那么做的努力肯定多, 那么更有可能挖到矿, 所以这个progress free是挖矿公平性的保证

> 比特币总量

比特币只能通过出块奖励凭空增加,而出块奖励是大概每隔4年(21w个区块)减半的,那么比特币的数量就构成了Geometric series

![Alt Text](/assets/images/2021-12-16/btc amount.png "btc总量")

因为比特币的总量有限,并且到最后是纯算力的比拼, 这在某种程度上保证了比特币的安全,所以说

BitCoin is secured by mining

因为在这种去中心化的系统中,挖矿提供了一种凭借算力投票的手段,只要大部分算力掌握在诚实的节点手里,那系统安全性可以得到保障

但是挖矿只能从概率角度保证记账权大概率落到诚实的节点手里,不能保证记账权绝对不会落入有恶意的节点手里

但是如果落入恶意节点手里,记录了不合法的交易,诚实的节点并不会认可这个交易,那么会继续拓展之前的最长合法链, 那么这个不合法的区块会变成孤儿区块, 所以攻击者的代价是极大的,因为不仅伪造的交易不能被接受,还得不到出块奖励

![Alt Text](/assets/images/2021-12-16/malicious block.png "不合法的区块被抛弃")

如果恶意节点企图回滚交易,那么为了成为最长合法链,往往需要连续获得好几个记账权才能做到篡改记录,比特币为了防范这种攻击,往往交易需要多等几个confirmation才会生效

比特币一般缺省状态下,需要等待six confirmation才保证前面这个交易是不可篡改的

![Alt Text](/assets/images/2021-12-16/confirmation.png "6次确认")

所以说区块链是不可篡改的账本(irrevocable ledger)

当交易发布出去,但是还没被写入区块链中,这个时间点,称之为zero confirmation

例如在转账完成之后,不用等待写入区块链,而是接收方委托一个全节点监听这个交易,确认交易的合法性之后,交易就可以完成了

这是一个比较普遍的交易,无需等待一个小时6个确认,但是并不会很大风险. 因为缺省状态下,在有交易冲突的时候,只接受最早接收到的交易信息,所以当交易先被发布出去之后,如果有恶意节点企图覆盖交易回滚,那么并不被节点所接受,因为冲突交易中恶意篡改的那个交易并不是第一个被接受到的.而且在处理交易的时候往往有天然的延迟,如果商家发现最终没有写进区块链,可以选择停止发货(这是体系之外的保障)

如果恶意的节点故意不写入某些合法的交易,问题也不大,因为下几个区块肯定有诚实的节点写入, 而且正常情况下BTC规定交易最大只能1MB, 如果交易太多了, 也只能被写入以后的区块里

> 额外: Selfish Mining

Selfish Mining: 挖着块攒着不发,多挖几个一起发出去了

优点: 不发布区块,可以提前挖下一个区块,减少竞争

风险: 可能并不能很快的挖出下一个区块,风险高