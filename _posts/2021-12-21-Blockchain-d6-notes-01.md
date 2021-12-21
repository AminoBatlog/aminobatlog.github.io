---
title: 区块链笔记 挖矿算法
key: blockchain20211221001
tags:
  Blockchain
  ETH
---

区块链笔记6_1:

挖矿算法

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

Block chain is secured by mining

但目前BTC的问题是挖矿设备专业化, 所以后续的货币设计都要努力做到ASIC resistance, 保证算力分散

有一种做法是增加计算对内存的需求, 就是memory hard mining puzzle, 因为ASIC对于普通的计算机来说, 算力强, 但是对于内存的访问并没有那么强

![img](/assets/images/2021-12-21/memory hard mining puzzle.png)

一个早期的例子LiteCoin, 用的puzzle是基于scrypt的, 一个对于内存要求很高的Hash函数. 具体思想是, 开启一个很大的数组, 然后按照顺序随机填写一个伪随机数, 但不是完全随机的, 前后两者是有关系的, 后者是前者求出来的, 然后解密puzzle的时候, 要读取一些数. 取出来的数经过一些运算之后, 得到下一个要取指的位置, 再经过运算得到下一个的位置... 这样做的好处是如果数组开的足够大, 那么就是memory hard的. 因为如果不存储整个数组, 每次求一个特定位置的值的时候, 都需要从头算一遍, 那么计算复杂度会大幅度上升

但也可以比如只保留奇数位置的数据, 偶数位的不要了, 如果用到偶数位的时候, 再计算出来, 这样内存少一半, 计算复杂度也没有高太多, 这种叫做time-memory trade off

这个好处是对矿工来说是memory hard的, 但是坏处是对轻节点来说, 也是memory hard的. 之前提到过设计puzzle的一个原则是difficult to solve, but easy to verify, 但是这个例子里验证puzzle所需要的内存区域和矿工所需要的是一样的.

结果导致LiteCoin在设计的时候, 不敢分配太大的空间, 然后实际使用的时候, 数组只有128k

虽然这个对LiteCoin后来的发展没有什么用, 依旧出现了GPU和ASIC挖矿, 但是解决了货币冷启动的问题, 因为这个理念一开始聚集了人气.



ETH也是用一种memory hard的puzzle, 但是和LiteCoin不一样

ETH用两个数据集, 一大一小, 小的是16M的cache, 大的是1G的dataset, 叫做DAG. 这1G是从16M生成出来的. 这么设计就是为了方便验证, 轻节点只需要保存16M就行了, 但是矿工需要保存1G.

小数据集生成方式和LiteCoin相似, ETH里是先从一个seed开始, 对seed取hash得到数组第一个位置的值, 然后对第一个取hash得到第二个, 以此类推. 小的数据集生成完之后, ETH并不是直接在小数据集上找值, 而是再生成一个大数据集. 大的数据集的每个元素, 都是从小的cache里, 按照伪随机的顺序, 读取一些元素. 和上面LiteCoin一样, 通过读取一个数, 计算过后找到读取下一个数的位置读取下一个数, 这样来回读取256次, 算出来的数放入DAG的第一个元素里, 然后DAG里的第二个元素同理

然后在求解puzzle的时候, 用的是DAG里的数, 通过伪随机的方式在DAG里读取128个数. 方法是:

一开始根据区块的header还有里面的nonce值, 算出一个初始的hash, 根据这个hash映射到大数据集中的某个位置, 然后再进行一些运算, 算出下一个读取的位置. 而每次读取的时候, 除了读取对应的值, 还要读取相邻的值, 所以每次读取的时候是读取2个相邻的元素, 这样循环64次后, 就读出了128个数, 最后算出一个hash值来, 和难度目标阈值比较一下看看是不是在target内的值, 如果不是的话, 把nonce替换掉, 然后重新计算

ethash的伪代码

**这个函数是生成**cache的函数

![img](/assets/image/2021-12-21/pesudocode of ethash 01.png)

这个函数是**生成DAG中的第i个位置的元素**

![img](/assets/image/2021-12-21/pesudocode of ethash 02.png)

这个函数是**生成整个1G的DAG的过程**

![img](/assets/image/2021-12-21/pesudocode of ethash 03.png)

这个函数是**矿工用来挖矿的函数和轻节点用来验证的函数**

![img](/assets/image/2021-12-21/pesudocode of ethash 04.png)

第一个header为块头信息, 第二个nonce为随机数信息, 第三个full_size 为DAG中元素的个数, 这个DAG每隔3w个区块就会增加1/128, 也就是8M的大小, dataset就是DAG

下面那个是轻节点用来验证的函数

header是被验证的区块header, nonce是这个区块的nonce, full_size仍然是DAG中的元素个数, cache是cache

因为轻节点没有保留大数据集, 所以验证的时候需要通过cache, 生成DAG中, 这个位置的元素, 因为DAG中每个位置的元素都可以独立生成出来, 所以验证要简单多了

这个函数是**矿工挖矿的函数**

![img](/assets/image/2021-12-21/pesudocode of ethash 05.png)

这个是整个流程的伪代码

![img](/assets/image/2021-12-21/pesudocode of ethash 06.png)



现在ETH挖矿, 以GPU的为主, ASIC的很少, 所以起到了ASIC resistance

矿工挖矿, 需要好几G的内存存放DAG, 就算是16M的cache也比128k大多了, 所以比LiteCoin好多了

ETH没有出现ASIC还有一个原因, 是因为ETH很久之前就打算从PoW转变成PoS, 这个对于ASIC生产有风险, 因为ASIC生产周期长, 如果ETH转入权益证明之后, 那投入的研发费用就白费了, 虽然目前为止还是PoW(2018年)

ETH当中采用的预挖矿(pre-mining)的过程, 当时刚开始的时候, 预留一部分货币给开发者

以太坊现状(2018年)

![img](/assets/image/2021-12-21/eth in 2018.png)



总结: 

在设计挖矿算法的时候, 要尽可能让通用计算设备能参与挖矿, 越多人挖矿, 算力越分散, 越安全

但也有人认为让通用计算设备挖矿不安全, 只有让ASIC专门挖矿才安全, 因为如果要发动攻击的话, 要花费大量的成本购置ASIC矿机, 而这些矿机除了挖特定的币, 没有任何用处, 所以成本很高. 就算攻击成功了, 那这个币种的安全性出问题了, 价格会跳水, 那获得的币就不值钱了, 那就更加回不了本了