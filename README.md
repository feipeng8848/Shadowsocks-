# Shadowsocks-
原文：https://teddysun.com/399.html
1）Shadowsocks 有几种版本？区别是什么？
首先要明确一点，不管 Shadowsocks 有几种版本，都分为服务端和客户端，服务端是部署在服务器（VPS）上的，客户端是在你的电脑上使用的。
Shadowsocks 服务端大体上有 4 种版本，按照程序语言划分，分别为 Python ，libev ，Go ， Nodejs ，目前主流使用前 3 种。
Shadowsocks 客户端几乎包括了所有的终端设备，PC ，Mac ，Android ，iOS ，Linux 等。
其实作者已经作了详细总结，包括 UDP 转发，多用户等 Feature ，具体可参考《Feature Comparison across Different Versions》一文，英文很简单，耐心一点，能看懂的。

2）Shadowsocks 的最低安装需求是多少？
个人建议最少 128MB 内存，因为在连接数比较多的情况下，还是占用不少内存的，如果内存不足，进程就会被系统 kill 掉，这时候就需要手动重启进程。当然，低于 128MB 也是可以安装的，Go 版是二进制安装，无需编译，非常简单快捷，libev 版运行过程中，占用内存较少，可以搭建在 Openwrt 的路由器上。
自己个人使用，且连接数不是特别大的情况下，64MB 内存也基本够用了。如果你要分享给朋友们一起使用，最好还是选用大内存的。

3）为什么我安装（启动） Shadowsocks 失败？
我只能说脚本并没有在所有的 VPS 上都测试过，所以遇到问题是在所难免的。大部分情况下，请参考《Troubleshooting》一文，自行解决。据我所知，很多人都是配置文件出了问题导致的启动失败。还有部分是改错了 iptables 导致的。
在 Amazon EC2 ，百度云，青云上启动失败，连接不上怎么办？
在这类云 VPS 上搭建，需要注意，配置服务器端时，应使用内网IP；Amazon EC2 缺省不允许 inbound traffic，需要在security group里配置允许连接的端口，和开通SSH client连接类似，这个在 Amazon EC2 使用指南里有说明。同样的，青云，百度云也差不多，默认不允许入网流量，网卡绑定的是内网IP，因此需要将配置文件里的 server 值改为对应的内网 IP 后再重新启动。然后在云管理界面，允许入网端口。
我帮人设置了过之后，才发觉这些云和普通的 VPS 不一样，所以需要注意以上事项。

4）Shadowsocks 有没有控制面板？
答案是有的，有人基于 PHP + MySQL 写出来一个前端控制面板，被很多人用来发布收费或免费的 Shadowsocks 服务。Github 地址如下：
https://github.com/orvice/ss-panel
具体怎么安装和使用，别来问我，自己研究去。

5）多用户怎么开启？
Shadowsocks 有多种服务端程序，目前据我所知只有 Python 和 Go 版是支持在配置文件里直接设置多端口的，至于 libev 版则需要使用多个配置文件并开启多个实例才行。
所谓的多用户，其实就是把不同的端口给不同的人使用，每个端口则对应不同的密码。Python 和 Go 版通过简单的修改单一配置文件，然后重启程序即可。

6）有没有必要简单学习 Linux ？
很有必要。
很多人问怎么修改配置文件，你用 winscp 连接上你的 vps ，把配置文件下载到本地，用记事本改吧改吧，改完后再上传覆盖一下不就完了么。
那为什么我还要建议你稍微懂一点 Linux 呢，说白了就是懂一点 Shell 命令。
看看这篇《Linux系统中常用操作命令》，在 putty 或者 xshell 的界面里，你用 vi 或者 nano 命令就能搞定配置文件，这样多好。
