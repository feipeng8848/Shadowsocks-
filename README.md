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

开启多端口
原文https://teddysun.com/532.html

一、Shadowsocks-Python
Shadowsocks-Python 版的配置文件路径 /etc/shadowsocks-python/config.json，下面以修改该配置文件来说明。
在 Linux 下建议使用 vim 或者 nano 来编辑此配置文件。具体如何使用这两种编辑器，这里不多说明，可自行去搜索相关用法。
Shadowsocks-Python 版多端口配置文件示例：

{
    "server":"0.0.0.0",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{
         "9000":"password0",
         "9001":"password1",
         "9002":"password2",
         "9003":"password3",
         "9004":"password4"
    },
    "timeout":300,
    "method":"your_encryption_method",
    "fast_open": false
}

重点在于 port_password 字段的修改。
你想要多少端口就添加多少端口，注意需要符合 json 格式，里面的最后一行后面是没有英文逗号的，整个大括号的最后需要有一个英文逗号。
修改完成后，保存配置文件，重启之。命令如下：

/etc/init.d/shadowsocks-python restart
二、ShadowsocksR
ShadowsocksR 版的配置文件路径 /etc/shadowsocks-r/config.json，下面以修改该配置文件来说明。
ShadowsocksR 版多端口配置文件示例：
```json
{
    "server":"0.0.0.0",
    "server_ipv6": "[::]",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{
        "9000":"password0",
        "9001":"password1",
        "9002":"password2",
        "9003":"password3",
        "9004":"password4"
    },
    "timeout":300,
    "method":"your_encryption_method",
    "protocol": "your_protocol",
    "protocol_param": "",
    "obfs": "your_obfs",
    "obfs_param": "",
    "redirect": "",
    "dns_ipv6": false,
    "fast_open": false,
    "workers": 1
}
```
重点在于 port_password 字段的修改。
你想要多少端口就添加多少端口，注意需要符合 json 格式，里面的最后一行后面是没有英文逗号的，整个大括号的最后需要有一个英文逗号。
修改完成后，保存配置文件，重启之。命令如下：
```bash
/etc/init.d/shadowsocks-r restart
```
三、Shadowsocks-Go
Shadowsocks-Go 版的配置文件路径 /etc/shadowsocks-go/config.json，下面以修改该配置文件来说明。
Shadowsocks-Go 版多端口配置文件示例：
```json
{
    "port_password":{
         "9000":"password0",
         "9001":"password1",
         "9002":"password2",
         "9003":"password3",
         "9004":"password4"
    },
    "method":"your_encryption_method",
    "timeout":300
}
```
重点在于 port_password 字段的修改。
你想要多少端口就添加多少端口，注意需要符合 json 格式，里面的最后一行后面是没有英文逗号的，整个大括号的最后需要有一个英文逗号。
修改完成后，保存配置文件，重启之。命令如下：
```bash
/etc/init.d/shadowsocks-go restart
```
四、Shadowsocks-libev
Shadowsocks-libev 版是唯一不能单纯靠修改配置文件来开启多端口的。
不过，开发者单独开发了一个 ss-manager 来管理和开启多端口，其工作原理大致如下：
调用 ss-server 并根据配置文件里的多个端口号，在当前用户目录下生成隐藏文件夹 .shadowsocks 以及拆分配置文件为 .shadowsocks_端口号.conf，并以此创建新的进程，再生成 .shadowsocks_端口号.pid 来保存进程的 pid 信息。
最终，创建出来的 ss-server 进程数和配置文件里的端口数相同。也就是说，每个端口需要开启一个 ss-server 进程。

于是就简单写了一个用于 Shadowsocks-libev 版多用户管理 ss-manager 的启动脚本，可以通过编辑 json 配置文件 /etc/shadowsocks-manager/config.json 的形式，启动和停止多端口的 libev 版服务端。
下面说一下用法。

1、下载该启动脚本并赋予执行权限。
wget -O /etc/init.d/shadowsocks-manager https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-manager
chmod 755 /etc/init.d/shadowsocks-manager

2、新建存放配置文件的目录
```bash
mkdir /etc/shadowsocks-manager
```
3、创建多端口配置文件，/etc/shadowsocks-manager/config.json 示例：
```json
{
    "server":"0.0.0.0",
    "port_password":{
         "9000":"password0",
         "9001":"password1",
         "9002":"password2",
         "9003":"password3",
         "9004":"password4"
    },
    "timeout":300,
    "user":"nobody",
    "method":"your_encryption_method",
    "nameserver":"8.8.8.8",
    "mode":"tcp_and_udp"
}
```
关于配置文件，更多选项，请参考：
https://github.com/shadowsocks/shadowsocks-libev/blob/master/doc/shadowsocks-libev.asciidoc

4、使用启动脚本
启动：/etc/init.d/shadowsocks-manager start
停止：/etc/init.d/shadowsocks-manager stop
重启：/etc/init.d/shadowsocks-manager restart
查看状态：/etc/init.d/shadowsocks-manager status

五、共通步骤
如果你是在 CentOS 系统里运行的，也许会需要将配置文件里对应的端口在防火墙里打开。
CentOS 6 一般是使用 iptables，先确认一下状态：
```bash
/etc/init.d/iptables status
```
如果是处于运行中，那么打开新端口的命令如下：
```bash
iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport 新端口号 -j ACCEPT
iptables -I INPUT -m state --state NEW -m udp -p udp --dport 新端口号 -j ACCEPT
```
保存并重启 iptables，命令如下：
```jbash
/etc/init.d/iptables save
/etc/init.d/iptables restart
```
显示 iptables 一览，并确认端口是否添加成功：

iptables -L -n
CentOS 7 一般是使用 firewalld，先确认一下状态：

systemctl status firewalld
如果是处于运行中，那么打开新端口的命令如下：

firewall-cmd --permanent --zone=public --add-port=新端口号/tcp
firewall-cmd --permanent --zone=public --add-port=新端口号/udp

重新加载 firewalld，命令如下：

firewall-cmd --reload
显示 firewall 一览，并确认端口是否添加成功：

firewall-cmd --list-all
Debian 或 Ubuntu 默认一般是不开启防火墙的，当然也有可能出现特殊情况已经开启了，那么同样需要将配置文件里对应的端口在防火墙里打开。
当然，如果你嫌麻烦，那么可以直接将防火墙关闭。
iptables 的关闭方法：

/etc/init.d/iptables stop
关闭开机自启动

chkconfig iptables off
firewalld 的关闭方法：

systemctl stop firewalld
关闭开机自启动

systemctl disable firewalld
另外，如果你使用的是大公司的 Cloud 产品，比如 AWS，Google Cloud，Azure，阿里云等等，也许还需要在后台的控制面板里将对应的通信端口打开。这里就不多说明了，每家的方法大同小异。
