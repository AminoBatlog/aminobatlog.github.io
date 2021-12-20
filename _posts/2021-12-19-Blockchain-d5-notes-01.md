---
title: 区块链笔记 交易树和收据树
key: blockchain20211220001
tags:
  Blockchain
  ETH
---

区块链笔记5_1:

交易树和收据树

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

> 交易树和收据树

ETH中除了状态树, 还有交易树和收据树

交易树记录每一笔交易, 而收据树记录每一次交易产生的一些信息, 所以他们是一一对应的, 但是因为智能合约比较复杂, 所以需要收据树来帮助记录一些信息以供快速查找之类的

交易树和收据树都是MPT, 这个和BTC不一样, BTC的交易用的只是普通的Merkle Tree, 而这里是用的Merkle Patricia Tree

原因可能是统一数据结构方便管理代码, 同样MPT方便查找, 对于状态树来说, 就是按照地址内容依次查找, 对于收据树和交易树来说, 就是交易在区块里排的序号, 交易顺序就由发布的节点决定

交易树和收据树都只是只把当前交易的数据收集起来的, 而状态树是把系统中所有的状态都包括进去

多个区块的状态树, 是共享节点的, 每次发布交易的时候, 只有被影响的状态树里的节点需要新建分支, 其他的都是沿用原来状态树上的节点

而每个区块的交易树和收据树是独立的, 不会共享节点(类似BTC)

用途:

提供Merkle Proof(类似BTC)



如果想要进行比较复杂的查询, 例如查询10天内有关某个智能合约的所有交易. 那ETH引入了一个数据结构叫做Bloom filter, 这个可以支持比较高效的查找某个元素是不是在一个比较大的集合里面

Bloom filter的思想: 给一个比较大的集合, 计算出一个很紧凑的摘要

比如用一个128bits的向量表示, 然后把集合里的元素都一一映射到这个向量里, 就可以形成摘要

![img](/assets/images/2021-12-20/collection digest.png)

所以当我们对一个新元素进行判断是否在集合中的时候, 只需要对齐进行Hash计算, 如果没找到, 那就说明一定不在, 但如果映射到了1的位置, 也不一定说明一定在, 有可能会碰到哈希碰撞. 所以用bloom filter的时候, 可能会出现false positive, 但是绝对不会发生false negative

Notes: 如果用的不是一个哈希函数, 而是用的一组Hash函数, 那某种程度上可以减弱false positive, 因为一个Hash函数发生碰撞, 不一定所有的Hash函数都发生碰撞

简单的Bloom Filter的一个限制条件就是, 不支持删除操作: 因为可能会出现集合里有Hash collision, 一个位置被多个元素映射到了, 不能够简简单单的把1置为0



ETH中Bloom Filter的作用:

收据里包含了Bloom Filter, 在区块header里也有一个总的Bloom Filter, 这个总的Bloom Filter是这个区块里所有的Bloom Filter的一个并集

当进行查找的时候, 首先去header里找Bloom Filter, 如果没有就是没有, 如果有, 就去收据树里找每个交易的Bloom Filter, 因为Bloom Filter存在false positive, 所以也有可能找不到. 但是仅仅只是利用这个性质, 过滤掉大量的无关的交易, 然后在剩下的候选区块里仔细查看



ETH的运行过程, 可以看做一个**交易驱动的状态机(Transaction-driven State Machine)**

状态指所有账户的状态, 通过执行交易, 可以使整个系统从一个状态, 转移到另一个状态

状态转移是确定性的, 对一个给定的状态, 和一组给定的交易, 能过确定性的转移到下一个状态



状态树里存储的状态是全部账户的状态, 仅仅只是说节点可以共用, 但是状态树是完整的账户状态

如果不使用完整的账户状态在状态树里的话, 就变成和BTC一样了, 需要不断追溯到之前的区块里的状态树去找保存了账户对应的数据, 更重要的是, 如果状态树不维护完整的账户状态, 一旦出现转账给新账户, 那么需要一路追溯到创世纪区块, 才知道被转账的是一个新账户



> 代码

代码中具体的数据结构:

![img](/assets/images/2021-12-20/header code.png)

在这串代码里, 有创建交易树和收据树的过程

![img](/assets/images/2021-12-20/transaction tree code.png)

首先判断交易列表是否为空, 如果为空的话, 那么根hash是一个空值

否则的话, 则通过一个叫做DeriveSha()的函数获取Hash值, 然后创建这个区块的交易列表

![img](/assets/images/2021-12-20/receipt tree code.png)

首先判断收据列表是否为空, 如果为空的话, 则根hash是个空值

否则的话, 同样也是通过DeriveSha()来获取Hash值, 然后创建块头里的Bloom Filter

![img](/assets/images/2021-12-20/uncle code.png)

首先判断是否有叔父区块, 如果没有则为空

否则的话, 通过调用CalcUncleHash()来计算叔父区块的Hash, 然后通过一个循环, 构建叔父区块里的数组(下一个内容会将)

![img](/assets/images/2021-12-20/DeriveSha function.png)

在这个DeriveSha函数中, 创建的是一个Trie Tree

![img](/assets/images/2021-12-20/MPT code.png)

而这个Trie Tree的数据结构, 则是MPT

![img](/assets/images/2021-12-20/receipt data structure.png)

这是receipt的数据结构

这里的Bloom就是 Bloom Filter, 这个logs是个数组, 每个收据可以有多个log, 这些收据的Bloom Filter, 就是根据这个logs产生出来的

![img](/assets/images/2021-12-20/block header data structure.png)

块头里的bloom就是刚刚看到的所有bloom filter合并得到的

![img](/assets/images/2021-12-20/deep explain receipt code.png)

所以这里是通过调用CreateBloom()来计算总的bloom filter

![img](/assets/images/2021-12-20/bloom function.png)

CreateBloom函数的参数是这个树中的所有收据, 通过循环, 获得每个收据的bloom filter, 然后再通过or 操作, 把每个bloom filter合并起来, 变成得到整个区块的bloom filter

LogsBloom函数的功能是生成每个收据的bloom filter

首先外层循环, 是把每个log的地址取Hash后加进bloom filter里, 这里的Bloom9是bloom的hash函数, 然后内层循环是把这个log中包含的每个topic, 加入到bloom filter里

Bloom9是bloom filter使用的Hash函数, 这里的方法是把输入映射到digest中的3个位置. 这里第一行调用crypto里的函数, 生成256位的hash value; 第二行为初始化r为0, r是最后要返回的bloom filter; 接下来的循环是把之前生成的32个字节的Hash Value, 取前6个字节, 每两个字节组成一组, 拼接在一起, 然后和2047进行位与运算(等同于对2048取余), 从而得到一个0-2047区间里的数. 这么做的原因是ETH中, bloom filter的长度是2048位. 循环的最后一行是把"1" 左移这么多位, 合并到前面的bloom filter里(类似于把对应映射的位置置为1), 这样进行3次循环, 把三个位置置为1的时候, 返回bloom filter

![img](/assets/images/2021-12-20/bloom filter search.png)

这里通过调用BloomLookup()函数实现查找有没有需要的topic, 首先用bloom9函数, 把要查找的topic,  转换成一个bit vector, 然后和bloom filter做与运算看看得到的结果是否和bit vector相等

