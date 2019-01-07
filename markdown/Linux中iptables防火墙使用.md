对于有公网IP的生产环境VPS，仅仅开放需要的端口，即采用ACL来控制IP和端口(Access Control List).

这里可以使用Linux防火墙netfilter的用户态工具

iptables有4种表：raw-->mangle(修改报文原数据)-->nat(定义地址转换)-->filter(定义允许或者不允许的规则)

**每个表可以配置多个链:**
* 对于filter来讲一般只能做在3个链上：INPUT ，FORWARD ，OUTPUT
* 对于nat来讲一般也只能做在3个链上：PREROUTING ，OUTPUT ，POSTROUTING
* 对于mangle是5个链都可以做：PREROUTING，INPUT，FORWARD，OUTPUT，POSTROUTING

**filter表的三个链详解：**
* INPUT链： 过滤所有目标地址是本地的数据包
* FORWARD链: 过滤所有路过本机的数据包
* OUTPUT链: 过滤所有由本机产生的数据包


举一反三学习：
```
【例】：过滤所有的访问：
iptables -t filter -A INPUT -s 0.0.0.0/0.0.0.0 -d X.X.X.X -j DROP

【例】：对SSH的22端口开放
iptables -I INPUT -s 0.0.0.0/0.0.0.0 -d X.X.X.X -p tcp --dport 22 -j ACCEPT

【例】：开放80端口
iptables -A INPUT -s 0.0.0.0/0.0.0.0 -d X.X.X.X -p tcp --dport 80 -j ACCEPT


【例】：来自124的数据禁止通过174 IP
iptables -A OUTPUT -p tcp -s 45.32.102.124 -d 157.240.22.174 -j REJECT 

【例】打印当前生效的iptables规则（-n显示IP地址）
iptables -L -n 
```




**Linux中iptables防火墙指定端口范围**

```
iptables -I INPUT -p tcp --dport 700:800 -j DROP 
iptables -I INPUT -s 11.129.35.45 -p tcp --dport 700:800 -j ACCEPT
```
一、 700:800  表示700到800之间的所有端口
二、 :800   表示800及以下所有端口
三、 700:   表示700以及以上所有端口
这个例子作用是，700-800端口，仅仅对 11.129.35.45 IP开放，白名单机制。

**Snat、Dnat的iptables用途：**
源地址转换(Snat): iptables -t nat -A -s 私IP -j Snat --to-source 公IP
目的地址转换(Dnat): iptables -t nat -A -PREROUTING -d 公IP -j Dnat --to-destination 私IP


**iptables命令详解**

```
iptables常用的命令选项有：
-P:设置默认策略的（设定默认门是关着的还是开着的）如：iptables -P INPUT (DROP|ACCEPT)
-F: FLASH，清空规则链的(注意每个链的管理权限)
-N:NEW 支持用户新建一个链,比如：iptables -N inbound_tcp_web 表示附在tcp表上用于检查web的。
-X：用于删除用户自定义的空链
-Z：清空链
-A：追加
-I num : 插入，把当前规则插入为第几条
-R num：Replays替换/修改第几条规则
-D num：删除，明确指定删除第几条规则
-L：查看规则详细信息，比如"iptables -L -n -v"
-s 表示源地址IP
-d 表示目标地址IP
DROP 表示丢弃(拒绝)
ACCEPT 表示接受
-p 表示适用协议,如tcp
```

其他更多例子：
```
【例】添加iptables规则禁止用户访问域名为www.sexy.com的网站。

iptables -I FORWARD -d www.sexy.com -j DROP

【例】添加iptables规则禁止用户访问IP地址为20.20.20.20的网站。

iptables -I FORWARD -d 20.20.20.20 -j DROP

【例】添加iptables规则禁止IP地址为192.168.1.X的客户机上网。

iptables -I FORWARD -s 192.168.1.X -j DROP

【例】添加iptables规则禁止192.168.1.0子网里所有的客户机上网。

iptables -I FORWARD -s 192.168.1.0/24 -j DROP

【例】禁止192.168.1.0子网里所有的客户机使用FTP协议下载。

iptables -I FORWARD -s 192.168.1.0/24 -p tcp –dport 21 -j DROP

【例】强制所有的客户机访问192.168.1.x这台Web服务器。

iptables -t nat -I PREROUTING -i eth0 -p tcp –dport 80 -j DNAT –to-destination 192.168.1.x:80

【例】禁止使用ICMP协议。

iptables -I INPUT -i ppp0 -p icmp -j DROP
```