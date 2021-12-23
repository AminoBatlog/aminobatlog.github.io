---
title: 区块链笔记 智能合约
key: blockchain20211223
tags:
  Blockchain
  ETH
---

区块链笔记8:

智能合约

笔记记录来源B站[北京大学肖臻老师《区块链技术与应用》公开课]https://www.bilibili.com/video/BV1Vt411X7JF

<!--more-->

---

智能合约是运行在区块链上的一段代码, 代码的逻辑定义了合约的内容

智能合约的账户保存了合约当前的运行状态

智能合约一般使用solidity编写的

![img](/assets/images/2021-12-23/smart contract auction sample.png)

solidity是面向对象的语言, 也是强类型语言

那么外部账户如何调用智能合约呢

![img](/assets/images/2021-12-23/invoke smart contract.png)

sender add是发起调用的地址, to contract add是被调用的合约地址, tx data给出被调用的函数, 如果函数有参数的话, 也是在data域里写明. 中间是一些参数

一个合约可以与另一个合约进行**直接调用**

![img](/assets/images/2021-12-23/direct invoke.png)

如果被调用函数抛异常, 发起调用的函数也会抛异常

第二种方法是**使用address类型的call()函数**

![img](/assets/images/2021-12-23/call invoke.png)

用call函数如果被调用的函数抛出异常, 就会返回false, 发起调用的函数并不会抛异常, 可以继续执行

第三种方法是**代理调用**

![img](/assets/images/2021-12-23/delegatedcall invoke.png)



标注payable: 以太坊规定如果这个合约要接受转账, 就需要有函数标注payable, 作用是出价之后把币锁定一段时间, 避免出现虚空出价

withdraw(): 拍卖结束后, 没有购买到的其他用户把锁定的钱取回来, 因为不是真的转账, 所以不用payable

**fallback函数**

![img](/assets/images/2021-12-23/fallback func.png)

如果fallback有接受转账的能力的话, 也需要标注成payable

如果没有定义fallback函数, 出现问题会抛出异常, 否则调用fallback



智能合约的创建和运行

![img](/assets/images/2021-12-23/smart contract initial and process.png)

发起一笔交易去0x0地址, 然后转账金额是0, 因为并不是想发起转账, 而是想借机发布一个智能合约

因为智能合约是图灵完备的, 那么我们该如何避免执行起来会出现死循环呢

![img](/assets/images/2021-12-23/gas fee.png)

这里涉及到一个halting problem, 就是不存在一个算法, 对于给定的任何一个程序, 判断出这个程序是否能停机, 是个不可解的算法

解决方法就是把这个问题推给发起人, 调用指令需要支付汽油费

AccountNonce是防止replay attack的, gaslimit是原意支付的最高汽油费, price是单位汽油的价格, recipient是收款人地址, amount是转账金额, payload是data域

全节点接受这个交易的时候, 先按照gaslimit. 一次性把所有的汽油费扣掉, 再根据实际情况退回剩余的gas fee, 如果不够的话会交易回滚

读取公共数据是免费的

交易的执行, 要不完全执行, 要不完全不执行, 所以不会执行到一半

所以当执行出现错误, 会导致整个交易的回滚. 如果出现用完了所有的汽油费之后没有执行完, 交易会被回滚, 汽油费不退

![img](/assets/images/2021-12-23/error.png)

在这个简单的例子里, 用require判断拍卖时间是否结束

**嵌套调用**

![img](/assets/images/2021-12-23/nested invoke.png)

如果使用的是直接调用, 那么就会导致连锁式回滚, 但如果使用的是call调用, 那仅仅只会在调用函数里返回一个false, 不会触发回滚

这里我们回顾一下之前提到的block header的数据结构

![img](/assets/images/2021-12-23/block header structure.png)

这里的GasUsed是这个区块所有交易消耗的汽油费加起来, GasLimit是这个区块所有交易能消耗的汽油的一个上限, 作为一个区块大小的限制

虽然ETH里也有GasLimit, 但是矿工可以对GasLimit进行微调, 在上一个区块的GasLimit的基础上上下调1/1024

**当一个全节点接收到一系列交易, 有些是对智能合约的调用, 那么这个全节点是要先执行完智能合约再挖矿呢, 还是先挖矿再执行智能合约?**

在一开始, 说执行只能合约的时候, 需要扣除全部汽油费, 在执行之后如有剩余加回去, 具体的操作方法是:

每一个全节点在收到这个交易的时候, 都在自己本地维护的状态树里扣除对应的汽油费, 只有当打包发上区块链之后, 本地的修改才会变成外部可见的

然后回到这个问题, 要先执行才能挖矿, 因为挖矿需要用到header, 而只有先执行, 才能知道header里的三个root hash, 更新了这三个hash之后, 才能够进行挖矿

**如果运行了智能合约, 又没挖到矿, 能得到什么补偿呢?**

没有补偿

**如果有矿工因为没有得到汽油费, 就不验证了继续挖怎么办?**

如果不验证, 那就无法保证区块链的安全性. 但是如果跳过验证的步骤, 就没法继续挖矿了, 因为如果不验证, 本地的三棵树内容无法更新, 就无法继续挖矿了. 发布的区块里没有三棵树的内容, 只有三个root hash, 所以为了获取内容还是得自己执行一次

**发布到区块链上的交易是不是都是成功执行的, 如果有错误的交易需不需要发布到区块链上?**

需要, 因为需要扣除汽油费. 而且其他节点也需要执行一遍看看你扣汽油费是不是对的

这里回顾一下receipt结构

![img](/assets/images/2021-12-23/receipt structure.png)

每个交易完成之后形成一个收据

status表示交易执行的情况

**智能合约是否支持多线程?**

solidity不支持多线程, 因为智能合约, 面对同一组输入, 所产生的输出, 必须是完全确定的. 因为所有的全节点必须做同样的操作到同样的状态, 作为验证. 多线程的问题是, 多个核对内存访问顺序不同的话, 执行结果有可能是不确定的

智能合约需要执行的结果是确定的, 所以无法通过一些系统的方式获得信息, 因为每个节点的环境是不完全一样的, 所以可以通过一些固定变量值, 得到一些状态信息

**下面是智能合约可以获得的区块信息**

![img](/assets/images/2021-12-23/block info that smart contract can know.png)

下面是智能合约可以获得的调用信息

![img](/assets/images/2021-12-23/invoke info that smart contract can know.png)

如下图例子

![img](/assets/images/2021-12-23/invoke info sample.png)

A调用了C1, C1里的f1调用了C2里的f2, 对于f2来说, msg.sender是C1账户, 因为是C1账户调用的f2, 但是对于tx.origin来说, 是A账户, 因为A账户是本次所有调用的发起者

这里的now和前面的区块信息里的block.timestamp是一样的东西

**下面是地址类型**

![img](/assets/images/2021-12-23/address type.png)

**三种发送Ether的方式**

![img](/assets/images/2021-12-23/3 ways to transfer ether.png)

回到最开始拍卖的例子

![img](/assets/images/2021-12-23/auction sample part 1.png)

拍卖的话, 多次出价只要付差价就行了, 比如第一次拍100ether, 第二次有人拍110ether, 然后再跟到120ether, 第二次出价只需要再打20ether就好了

下面是构造函数, 创建合约的时候记录受益人和拍卖结束时间

接下来是拍卖用的两个函数, 左边的是竞拍的时候用的, 右边是结束拍卖的时候

![img](/assets/images/2021-12-23/auction sample part 2.png)

竞拍函数不需要传参, 因为在发起交易的时候已经表明了转账金额, 只需要用msg.value就可以查看到出的价格了. 如果没有出过价的话, bids[msg.sender]返回0, 下面需要把sender压紧bidder数组里, 因为solidity的哈希表不支持遍历, 所以需要额外创建一个数组记录有谁

右边的拍卖结束的函数, 第一个是判断拍卖时间是否结束, 第二个是判断这个函数有没有已经被调用过了

这个函数的问题在于

如果有个黑客调用了左边的合约参与拍卖

![img](/assets/images/2021-12-23/auction hacker.png)

参与拍卖的时候没有问题, 但是退款的时候就会变成退款到黑客的智能合约账户上了, 而不是正常人的外部账户. 就会导致当合约账户收到转账的时候, 会调用fallback函数, 但是这个黑客合约里没有fallback函数, 就会抛出异常, 而transfer函数会导致连锁回滚, 导致无法完成退款操作. 就会导致所有参与拍卖的包括发起拍卖的都收不到退款

如果出现了这种情况, 就这个拍卖机制而言, 没有办法, 所以code is law, 没人能篡改, 但也没人能修bug. 就像是irrevocable trust. 所以提价智能合约的时候, 可以在专门的testnet上进行测试, 确认没问题了才能发布

**能不能在合约里留后门用于修复bug?**

这样做的前提是所有人都信任这个系统管理员, 与去中心化的理念背道而驰, 也是绝大多数区块链的用户不能接受的

所以有第二个版本的拍卖

![img](/assets/images/2021-12-23/2nd auction.png)

左边的函数是由拍卖者自己调用函数拿回自己的存款, 右边的函数是把最高出价给受益人.

这个操作把退款分成了两部分

这样的版本有另一个问题, 就是重入攻击(Re-entrancy Attack)

![img](/assets/images/2021-12-23/re-entrancy attack.png)

在右下角这个黑客的fallback函数里, 又调用了一次withdraw函数, 而转账到合约账户的时候是会调用fallback函数的, 而在原来的withdraw函数里, 是先退款再清零的, 而在退款的期间又进行了一次调用的话, 还没执行到清零函数, 就又调用了一次, 结果就可以一直withdraw

结束的条件有:

1. 拍卖的合约账户里没有余额了
2. 汽油费不够了
3. 调用栈溢出了

所以在fallback里, 对余额, 剩余汽油, 调用栈的深度, 都进行了一次判断

那么解决方法就是刚刚的Pay2Beneficiary的做法, 先清零, 再转账

所以一个经典的合约资金交互模式就是: **先判断条件, 然后改变条件, 然后再发生交互, 在区块链上任何未知的合约, 都可能是有恶意的**

另一种修改方法是

![img](/assets/images/2021-12-23/withdraw modified.png)

首先是吧清零的位置提前了, 先清零再转账, 而且转账用了send. 这里的好处是通过send发送的汽油费只有2300个, 不足以支持被发送合约发起调用, 只够写一个log