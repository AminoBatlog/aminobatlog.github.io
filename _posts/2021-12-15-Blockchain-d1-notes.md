---
title: 区块链笔记: 密码学原理和数据结构
key: blockchain20211215
pageview: true
comment: true
tags:
  Blockchain
---

区块链笔记1:

密码学原理, 数据结构

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

> 密码学原理
>
> > Hash

比特币等加密货币被称之为crypto-currency

比特币主要用到了密码学忠的两个功能:

1. 哈希
2. 签名

密码学中用到的哈希函数被称之为Cryptographic hash function, 有两个重要的性质

**Collision Resistance**: 

当两个不同的信息x,y通过哈希函数计算出来的结果是H(x) = H(y)时,那么就发生了哈希碰撞

哈希碰撞是不可避免的,因为输入空间总是大于输出空间,但是安全的点在于,给你一个x,你很难找到一个y,从而使得H(x)=H(y),除非进行brute-force

哈希碰撞无法人为制造,这是无法通过理论角度证明的,都是根据实践经验得来(例如MD5在实践中发现可以被人为进行哈希碰撞)

**Hiding**:

哈希函数的计算是单向的,无法通过H(x)计算出x

前提是输入的空间足够大,结果分布是均匀的,如果输入空间不够大,那么可以拼接一些随机数,例如H(x||nonce)

该性质可以结合前者,实现digital commitment (digital equivalent of sealed envelope):

例如将预测结果x进行计算,公布H(x),等结果出来之后再公布预测结果x,可以比对之前公布的H(x)来保证这个预测结果没有被篡改

除了这两个密码学性质之外,还有另一个性质:

**Puzzle Friendly**:

指哈希计算结果是不可预算的,例如需要得出前k位为0的哈希值,那么没有捷径,只能够一个一个计算直到得出能落到要求范围内的哈希值,所以这个过程可以被用作工作量证明(proof of work)

虽然计算很难但是验证很容易仅仅只需要一步计算(difficult to solve, but easy to verify)

比特币中用的哈希函数叫做SHA-256

> > Asymmetric Encryption Algorithm

在比特币系统中开创账户不需要找任何第三方,因为区块链是去中心化的,所以仅仅需要在本地创立一个公私钥匙对

**信息传递原理**:

当Alice需要传递信息给Bob,仅需使用Bob的公钥进行加密,那么只有Bob用私钥解密才能看到plaintext信息,虽然公钥是发放到互联网上的,但是私钥是本地保存的

但是当Alice向Bob转了10个bitcoin,如何证明这笔交易是有Alice发起的呢

**消息验证原理**:

Alice用自己的私钥进行签名,那么这笔交易任何人都可以用Alice的公钥进行验证(私钥加密,公钥验证)

如果产生公私密钥对使用的随机源是个好的随机源(a good source of randomness),那么几乎不可能碰撞出同样的公私密钥对,如果使用的随机源不好,那么所有的努力全部木大

---

> 数据结构
>
> > Blockchain

比特币的基本结构就是区块链,区块链就是一个一个区块产生的链表,与普通的链表有些许差异:

**用哈希指针(Hash Pointer)代替了普通指针**:

区块链的第一个区块叫做创世纪块(genesis block),最后一个区块是最近产生的区块(most recent block),每一个区块都包含一个指向前一个区块的哈希指针

哈希指针的内容: 将前面区块的所有信息,包括前面一个区块指向前面的前面的区块的哈希指针,进行哈希计算

这种结构的好处就是可以实现temper-evident log,只要有个一区块的内容被篡改,那么那个区块的下一个区块的Hash pointer,以及下下区块的Hash pointer,以及下下下个区块...一直到最后一个hash pointer,都会被篡改

![Alt Text](/assets/images/2021-12-15/blockchain structure.png "区块链结构示意图")

因此比特币没有必要保存所有的区块,只需要保存最近几千个区块就好了,如果需要前面的区块,只需向其他节点请求.如果其他节点发送了被恶意篡改的区块,也是可以通过计算哈希值和现有的hash pointer比较确认的

> > Merkle Tree

Merkle tree是类似于B树的一种数据结构

![Alt Text](/assets/images/2021-12-15/merkle tree.png "Merkle Tree示意图")

其中最下面一层是数据块,最上面三层则都是哈希指针,第一层是根节点,对根节点取哈希,得到root hash

这种结构同样是只要记住root tree,就能验证任何部位的修改

Merkle Tree的作用: 提供Merkle Proof

比特币中节点氛围两类,一类是全节点(包含整个区块的内容),一类是轻节点(只有块头,只有hash)

如何证明一个Tx已经被写入了区块链?

证明过程:

![Alt Text](/assets/images/2021-12-15/merkle proof.png "Merkle proof示意图")

当验证黄色tx是否存在的时候,会向全节点请求红色部分的Hash, 然后本地对求证的Tx进行哈希计算,得到最下方的绿色Hash,然后与最下面的红色Hash结合进行计算,得到第二层的绿色Hash,然后再和提供的第二层的红色Hash计算,以此类推一直到最后计算出root hash,再和已有的root hash进行比对求证

那么存不存在我篡改了黄色的Tx之后,手动修正Hash来避开验证呢,根据collision resistance,没有办法能够手动认为制造出想要的哈希值,所以不可行

如何证明里面没有某个交易(proof of non-membership),首先需要对交易内容的哈希值进行一次排序,然后找出我们要验证的交易在哪个区间里,然后拿相邻的两个交易出来进行计算验证,如果这两个交易验证出来确实是相邻的两个叶子,那么就证明这个交易不在这个树里

---

保留疑问:既然已经可以把交易的哈希值排序了,为啥就不能进行查找呢

初步猜想:可能是因为要计算到最后root hash来比较保证这两个交易确实在树里?所以终究还是得进行Merkle Proof?

