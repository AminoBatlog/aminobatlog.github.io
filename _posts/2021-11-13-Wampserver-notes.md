---
title: Wampserver搭建php环境+WordPress配置
tag: Web
---

在自行搭建wamp环境的时候，因为MYSQL的版本太高导致WordPress无法连接到数据库，后来想了想干脆找个集成的软件直接一键搭建环境好了（其实是懒得再去重新下一遍mysql重新配置了），刚好家里的狐狸想要整个网站导航网页，然后我在WordPress上找到了个不错的模板，就顺便记录一下整个搭建的过程

不过之前的手动搭建WAMP也算是成功了，有空再写个独立的blog来讲述手动搭建WAMP的过程，今天着重点还是类似于一个如何一条龙使用WordPress的记录吧
<!--more-->

---

> Wampserver下载和安装

集成环境我选的是Wampserver，一开始我选了phpstudy v1.8，结果那个服务器有问题死活没法进行更新，可惜了一个功能蛮齐全的软件，最后没法用只能选择Wampserver了。通过访问[WampServer, la plate-forme de développement Web sous Windows - Apache, MySQL, PHP](https://www.wampserver.com/en/)

来进入到wampserver的官网，然后去到下方的**DOWNLOADS**大标题那儿，选择合适的版本进行下载，64位就选X64，32位就选X86。

下载完之后直接双击安装，注意**路径要纯英文**，特别是在安装一些非中国人写的程序的时候，最好养成英文路径的习惯，不然哪天程序坏了要debug半天得哭死x

安装完了之后，整体的环境就搭建完了，那么接下来是对wampserver的系统托盘图标的一些操作：

> > 左键wampserver图标

![Alt text](/assets/images/2021-11-13/1.png "wampserver界面")

在这个界面可以看到Apache的版本，PHP的版本，还有MYSQL的版本

根据WordPress的文档，**PHP要求是7.4以上**，**MYSQL是5.6**， 或者是用**MariaDB10.1以上**的版本，同时还要支持HTTPS（我写到这里才发现我的PHP是7.3的，回头我得去更新一下）

参考网址：[要求 | WordPress.org 香港中文版](https://zh-hk.wordpress.org/about/requirements/)

要是像我一样发现版本不对也不用担心，在这个界面可以直接点PHP，然后选择7.4的版本，在安装界面默认安装的时候是选择了7.4的版本

![Alt text](/assets/images/2021-11-13/2.png "PHP版本选择")

<u>在这里，我个人建议不使用太高版本的MYSQL，符合要求就行</u>

然后这个界面剩下的部分就是例如localhost可以一键进入网页，start, stop, restart all services可以一键启动，停止和重启服务。当然，对应的服务也可以手动进入Windows的服务界面手动启动之类的（不过也没必要在有小托盘的前提下还要自己手动去Windows设置里点）

同样的，在这个界面里也可以一键访问MYSQL console，还有其他的等等设置，具体就各位感兴趣自行研究下了x

> > 右键Wampserver图标

右键的话比较偏向于偏好设置和工具之类的，这里就简单介绍几个可能会用到的功能

![Alt text](/assets/images/2021-11-13/3.png "Wamp 工具")

在Wamp工具里，提供了一些比较方便的一键操作，例如在最顶上的Tools的tab里，有重启DNS，检查httpd.conf语法（如果像我一样比较习惯性主动去找文件修改的话）。在Port used by Apache:80里，可以对80端口进行测试，修改端口号等等。在Default DBMS里可以切换MYSQL和MariaDB。还有其他一系列指令感兴趣的可以慢慢看。

> > Wamp图标

WAMP总共会启动三个服务，分别就是Apache，MariaDB和MYSQL，具体的服务也已经注册在Windows里了，找wamp开头的就行。当这个小图标是**绿色**的时候，代表**所有服务正在正常运行**，当小图标是**红色**的时候，代表**所有服务停止运行**。最麻烦的是碰到**橙色**图标，**数据库服务正常运行但是Apache服务崩掉了**，具体的可能出现的原因还不是很清楚，我当时是直接重装了Wampserver，反正数据保留着弄完再替换回来就行了x

---

好那么到这一步，WAMP环境算是配置完成了，也确认选中了能支持WordPress的环境之后，那么可以进一步安装WordPress了，WordPress安装过程非常简单

> WordPress安装

首先，先去下载WordPress（废话）下载地址：[下载 | WordPress.org China 简体中文](https://cn.wordpress.org/download/)

下载完之后直接解压，然后覆盖回wamp的www目录（在这一布会替换掉index.php，直接替换就行了，要是想保留原来的index.php可以把原来的index.php改个名字放旁边，没有问题）

完事之后可以直接在浏览器里输入localhost，进入5步安装界面

不过在此之前，还得先对数据库进行一些配置

> 数据库配置（MYSQL）

由于我个人比较偏好mysql，所以在这里我就只拿mysql举例子了

首先，可以选择下载个mysql workbench，用图形化界面进行操作，因为我mysql就只上了一门database fundamental的课，所以对命令行不是很熟悉，我就选择了workbench来帮我操作。下载链接在这里：[MySQL :: Download MySQL Workbench](https://dev.mysql.com/downloads/workbench/)

下载安装完之后，打开添加链接，Wampserver默认的mysql账号是root，然后密码是没有密码，就添加链接：

| 选项     | 参数      |
| -------- | --------- |
| hostname | 127.0.0.1 |
| port     | 3306      |
| Username | root      |

剩下的都是留空

登录进去之后，为了安全考虑，先进行一个密码的设

![Alt text](/assets/images/2021-11-13/4.png "Administration界面")

在这个界面里，点击**Users and Privileges**，可以看到你所有的用户，点击root，然后修改**Password**，然后把密码在输入一遍放进**Confirm Password**，最后点右下角的**Apply**，密码设置完成

在这一步先别着急退出，继续在这个界面里点击左下角的**Add Account**，因为不建议直接使用root账户进行WordPress的数据库管理，这里推荐创建一个新的账户用于管理，名字随意。

点击了Add Account之后，设置**<u>login Name</u>**，还有**<u>Password</u>**，然后点击**Apply**。这两个参数得记住，等会再WordPress界面要用到。

这一步用户创建完了之后，继续回到坐标的**Navigator**界面，点击下面的**Schemas**，然后右键空白处点击**Create Schema...** 按钮，创建一个给WordPress使用的数据库。数据库名字随意，然后点**Apply**，在弹窗里继续点**Apply**，其他的参数不用改。之后便创建了一个用于WordPress的数据库了，同样的，这个**<u>数据库的名字</u>**我们也得记住，等会WordPress设置要用。

然后我们回到刚刚的**Administration > Users and Privileges**界面，鼠标单机选中刚刚创建的新用户，在中间的设置界面里，找到**Schema Privileges**，点击**Add Entry...** 给这个用户添加一下权限。在弹出来的窗口中，在**Selected schema**里选中刚刚创建的数据库名字，然后点击OK，就能看到一个Schema是数据库名字，但是旁边的Privileges里毛都没有的界面。

接下来选中对应的Schema，在下面的**Objects Rights**里，点击**Select "ALL"**，然后点击**Apply**

在这里，我们是完成了数据库的创建，用户的创建，以及对用户权限的分配。接下来可以回到localhost界面进行WordPress的配置了

> WordPress 数据库链接配置

进入localhost之后，就能看到WordPress的五步配置界面了，进入配置界面之后，他需要你输入**数据库名字**，**数据库用户名**，**数据库用户密码**，还有**数据库前缀**，前三个便是在上文提到的三个加粗下划线的东西，最后一个是可选的，如果想多配置几个wordpress的话，可以自定义一个前缀，这样你以后同样的服务器多配置几个WordPress站点的时候不会互相冲突了

如果这一步之后没问题了，那么恭喜你你已经完成了WordPress的站点配置，剩下的部分就是创建管理员账号之类的，就按照提示一步步来就好了

然后想使用WordPress模板的话，在后台界面的主题设置那里可以选择手动导入主题，就把你下载的模板直接导入进去，然后点启用就可以了

---

> 后记

在配置过程中，可能会碰到各种奇奇怪怪的问题，毕竟每个人的服务器环境不尽相同，我也就只是用一台服务器进行了一次配置，如果碰到问题，还记得要利用搜索引擎积极探究，毕竟这也是自己搭建各种骚东西的乐趣之一，就是不断发现问题，解决问题。

如果大家觉得这篇文章有帮助，欢迎大家点赞分享给需要的人，如果觉得这篇文章有地方说错了，也欢迎大家评论区指正（博客评论区暂时没开放，可以先用b站的评论区），这篇文章是我配置完之后凭记忆写出来的，可能有些细节部分被我落下了，欢迎补充

Wish y all peace