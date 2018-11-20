**Linux Ubuntu系统之PPP拨号经验分享**

近期，工作需要，我负责开发PPP拨号模块。
说起拨号，算算时间，我已经做过2次了, 暴露年龄了，呵呵。

>第一次是刚毕业做的PPOE拨号，给电信做拨号软件，在河北石家庄工作过一段时间，基于windows xp。

>第二次是在移动网优，3G手机路测，即著名的TD-SCDMA，基于AT指令控制手机驱动。

这次，是用的PPPD拨号，在Linux系统下。
pppd 拨号模块，Linux系统是自带的, 就像windows下自带的RAS拨号一样，打印机等很多应用需要通过拨号方式进行通信的。

>Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-161-generic x86_64)
pppd 2.4.5

参考文档，配置4个文件：

/etc/ppp/peers/myvpn 账号信息
```
remotename myvpn
linkname myvpn 
ipparam myvpn
pty "pptp *** --nolaunchpppd --loglevel 0"
lock
nodeflate
name ***
usepeerdns
require-mppe
noauth
require-mppe-128
defaultroute
mtu 1416 #特别关键！！！
```

/etc/ppp/chap-secrets VPN用户名密码
```
user pass
```

/etc/ppp/options 默认设置项
```
lcp-echo-failure 10 # (from /etc/ppp/options)
lcp-echo-interval 10 # (from /etc/ppp/options)
lock
crtscts
nodeflate
persist
asyncmap 0
noauth
hide-password
noipx
```

/etc/ppp/options.pptp 设置项
```
lock
noauth
refuse-pap
refuse-eap
refuse-chap
refuse-mschap
nobsdcomp
nodeflate
require-mppe-128
ipparam myvpn
defaultroute
```

**个人总结的技巧：**
- 一定要升级python3.4 --> python3.7?

我开始很纠结Python版本，代码开发是Python3.7最新版，而Ubuntu自带的是Python 3.4, 故想办法升级python3.7，如果在本地网速很快，这个不是什么难事，1小时工作量。

但是，远程链接SSH，VPS服务器在国外，网速卡的厉害，本来1小时工作，忙乎了一个上午才搞定，升级到python3.6 + pip3 。 但是一想，我还有n个服务器呢，故晚上加班把代码降级为pyhon 3.4，这样部署就方便多了 -- 非原则问题，不要在环境上折腾太久，条条大路通罗马嘛。

这个事情，给我很大的启示：不要做战略的矮子，再勤劳的执行力, 团队的效率也上不来的。

**平衡、成本、决策！**

- 部署python程序，background job running

windows开发C#很多年，除了前几年做Unity3D开发的游戏APP（含VR、AR），这些都是有GUI界面的，而在Linux下，第一个门槛就是无UI界面。
调试程序通过，部署后，我关闭ssh下班了，吃完饭，远程ssh，怎么我的python程序不见了，惊讶不已，才***行代码，而且我写的是 while true 循环，不可能自己退出啊。
nohup python3 main.py &
ps ax | grep py

上网搜索，多亏google，很快就明白了，SSH通过22端口，开启了一个“session”，一般，如你执行 python3 main.py，随着SSH Session结束，Linux会kill这个process的。 而这个PPP拨号程序需要作为一个长时间运行的，故需要用 nohup 和 & 关键字，这样当你退出ssh，这个程序会驻留系统。

那么问题来了，查询运行的process，常用的 ps all就是不灵了。

要用 ps ax | grep py 才可以。

- linux常用工具工具

    - [ ] vi 编辑器，linux运维必备神器！
    - [ ] cat /var/log/syslog | grep pppd #输出mylog.log, search pppd
    - [ ] cat /var/log/syslog | tail -n 100 #输出mylog.log 文件最后100行
    - [ ] egrep -v '#|^ *$' /etc/ppp/options #正则，列出配置文件起作用的
    - [ ] * ">" /var/log/syslog #clear syslog
    - [ ] * dhclient -v -4 : refresh network #重新获得IP.

**参考文档：**
1. https://superuser.com/questions/949520/wvdial-ppp0-and-setting-default-route-automatically
2. https://askubuntu.com/questions/891393/vpn-pptp-in-ubuntu-16-04-not-working
3. http://www.cnblogs.com/simonshi/archive/2010/04/23/1718984.html