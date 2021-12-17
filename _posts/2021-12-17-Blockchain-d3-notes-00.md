---
title: 区块链笔记 比特币脚本
key: blockchain20211217
tags:
  Blockchain
---

区块链笔记3:

比特币脚本

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

> 比特币脚本

比特币脚本用的语言比较简单,能访问的就只是个堆栈

![Alt Text](/assets/images/2021-12-17/input scripts.png "输入脚本")

**交易列表结构的metadata**

![Alt Text](/assets/images/2021-12-17/Tx structure.png "交易结构")

Notes:

locktime: 交易的生效时间, 这里为0, 表示立即生效

blockhash: 记录当前区块的hash, 因为交易信息不参与hash计算, 所以可以被写入这个数值

time: 是交易产生的时间

blocktime: 是区块产生的时间

**交易的输入结构**

![Alt Text](/assets/images/2021-12-17/Tx input.png "交易输入结构")

Notes:

txid: 这里的txid不再是当前交易的id, 而是这笔交易锁用的币的来源Tx的id

vout: 是那个Tx的第几个输出

ScriptSig: 签名, 一个输入就是需要签名来证明是你的操作的, 所以叫做脚本签名

**交易的输出结构**

![Alt Text](/assets/images/2021-12-17/Tx output.png "交易输出结构")

Notes:

value: 交易金额

n: 序号,表示第几个输出

scriptPubKey: 一个脚本的输出就是需要公钥的, 所以叫这个名字

reqSigs: 表示这个交易需要多少个签名才有效

type: 输出类型, 这里都是公钥哈希

于是乎, 在交易的过程中, 总体的结构呈现如下的结构

![Alt Text](/assets/images/2021-12-17/Tx.png "交易")

如果执行过程中没有问题, 那么交易就是合法的, 反之不合法

如果有多个输入的话, 每个输入都需要匹配对应的输出验证

**几种交易形式**:

**P2PK (Pay to Public Key)**

![Alt Text](/assets/images/2021-12-17/P2PK.png "P2PK")

在这里直接给出收款人的公钥, 然后再加一步检查签名的步骤. 这里是直接对整个交易进行签名的

这是最简单的, PK直接在脚本里给出

执行过程如下

![Alt Text](/assets/images/2021-12-17/P2PK exe.png "P2PK execution process")

实际代码中出于安全考虑, 输出输入其实是分开执行的

第一条语句把输入里的签名压入栈, 第二条语句把公钥压入栈, 第三条弹出上两个, 用第二条语句压入的公钥检查第一条语句压入的签名是否正确, 进行验证

Notes: 这里被压入栈的公钥不是当前交易的收款人的公钥, 而是付款人币所来源的那个Tx的收款人公钥(也就是通过币来源的Tx, 获得付款人的公钥信息, 用于验证当前交易时付款人用私钥提供的签名是否正确)

实例如下

![Alt Text](/assets/images/2021-12-17/P2PK sample.png "P2PK sample")

第二种形式是最常用的: **P2PKH (Pay to Public Key Hash)**

![Alt Text](/assets/images/2021-12-17/P2PKH.png "P2PKH")

区别在于, 在输出脚本里没有提供公钥, 但是在输入脚本里, 需要提供公钥

![Alt Text](/assets/images/2021-12-17/P2PKH exe.png "P2PKH execution process")

在这里, 和上面的P2PK一样, 是按顺序执行的

第一步: 把签名压入栈

第二步: 把公钥压入栈

第三部: 把栈顶元素复制一遍(也就是公钥)

第四步: 把栈顶元素弹出取hash, 再压入栈

第五步: 把公钥哈希压入栈

第六步: 弹出栈顶2个元素, 比较是否相等(作用, 防止有人冒名顶替, 用自己的公钥伪装成别人的公钥)

第七部: 验证签名(和P2PK)一样

实例如下

![Alt Text](/assets/images/2021-12-17/P2PKH sample.png "P2PKH sample")

最后一种: **P2SH(Pay to Script Hash)**

![Alt Text](/assets/images/2021-12-17/P2SH.png "P2SH")

这里输入脚本提供的不是收款人公钥的信息, 而是一个收款人提供的脚本的信息,叫做redeemscript

这里举一个用P2SH实现P2PK的例子

![Alt Text](/assets/images/2021-12-17/P2SH implementation.png "P2SH实现P2PK")

首先还是像之前一样, 拼接输入和输出脚本

![Alt Text](/assets/images/2021-12-17/P2SH stage 1.png "P2SH验证第一阶段")

第一步: 把输入的签名压入栈

第二步: 把赎回脚本压入栈

第三步: 把赎回脚本取Hash(成为RSH)

第四步: 把输出脚本给出的Hash压入栈

第五步: 比较Hash是否相等

接下来进行第二阶段的验证

![Alt Text](/assets/images/2021-12-17/P2SH stage 2.png "P2SH验证第二阶段")

首先对序列化赎回脚本进行反序列化, 然后进行赎回脚本

第一步: 压入赎回脚本提供的公钥

第二部: 检查签名

P2SH的出现,是为了多重签名服务

早期多重签名(已经不推荐) **Multisig**

![Alt Text](/assets/images/2021-12-17/multisig.png "多重签名")

第一个x,是个无用元素, 因为checkmultisig方法的bug, 会多弹出一个元素, 所以最开始需要手动写入一个无用的元素, 因为区块链是去中心化的, 要是想改这个bug需要进行硬分叉, 代价太大了, 就没法改了

这里M是需要多少人的签名, N是总共有多少人的签名, 那么在这里的意思是只要拥有这N个人签名中的M个, 那么交易就可以执行

![Alt Text](/assets/images/2021-12-17/multisig exe.png "多重签名执行过程")

如图依次压入栈, 最后直接执行checkmultisig

**利用P2SH实现多重签名P2SH Multisig**:

![Alt Text](/assets/images/2021-12-17/P2SH multisig.png "P2SH实现多重签名")

在这种情况下, 输出脚本里的多重签名规则被打包起来了, 作为用户只需要知道在付款的时候输入什么样的Hash就行了, 无需关系内部具体的操作. 而后续如果收款方更改规则, 所需要的也只是公布一个新的RSH, 然后让付款方更新RSH就行了

**Proof of Burn**:

这个脚本开头是return, 意味着无论input里写了什么,执行到这里通通返回false, 所以这个Output无法被花出去, 其对应的UTXO也被剪枝了, 相当于销毁了比特币

![Alt Text](/assets/images/2021-12-17/Proof of Burn.png "Proof of Burn")

使用场景:

1. 小币种, 要求销毁一定数量的BTC,才能得到这个币种, 这种小币种叫做AltCoin
2. 往区块链里写内容, 可以把要保护的内容写入到return的后面,这样可以被永久写入链中

但是如果把所有转入的费用都用作交易费支付出去, 那么就不会销毁任何的BTC, 也可以把内容写入链中了, 例如如下实例

![Alt Text](/assets/images/2021-12-17/proof of burn sample.png "付钱给记账节点写入数据")