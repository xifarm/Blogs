所以对于有公网IP的VPS，生产环境，我的建议是仅仅开放需要的端口，即采用ACL来控制IP和端口(Access Control List).

Linux防火墙netfilter的用户态工具

iptables有4种表：raw-->mangle(修改报文原数据)-->nat(定义地址转换)-->filter(定义允许或者不允许的
nat：定义地址转换
mangle:修改报文原数据
)

每个表可以配置多个链:
 对于filter来讲一般只能做在3个链上：INPUT ，FORWARD ，OUTPUT
对于nat来讲一般也只能做在3个链上：PREROUTING ，OUTPUT ，POSTROUTING
mangle是5个链都可以做：PREROUTING，INPUT，FORWARD，OUTPUT，POSTROUTING

filter表的三个链：
INPUT链： 过滤所有目标地址是本地的数据包
FORWARD链: 过滤所有路过本机的数据包
OUTPUT链: 过滤所有由本机产生的数据包

过滤所有的访问：
iptables -t filter -A INPUT -s 0.0.0.0/0.0.0.0 -d X.X.X.X -j DROP

对SSH的22端口开放
iptables -I INPUT -s 0.0.0.0/0.0.0.0 -d X.X.X.X -p tcp --dport 22 -j ACCEPT

开放80端口
iptables -A INPUT -s 0.0.0.0/0.0.0.0 -d X.X.X.X -p tcp --dport 80 -j ACCEPT

-s 表示源地址IP
-d 表示目标地址IP
DROP 表示丢弃(拒绝)
ACCEPT 表示接受
-p 表示适用协议,如tcp

定义一条规则：不允许172.16.0.0/24的进行访问。

iptables -t filter -A INPUT -s 172.16.0.0/16 -p udp --dport 53 -j DROP 


iptables -A OUTPUT -p tcp -s 45.32.102.124 -d 157.240.22.174 -j REJECT 
iptables -A OUTPUT -p tcp -s 45.32.102.124 -d  157.240.13.52 -j REJECT 
iptables -A OUTPUT -p tcp -s 45.32.102.124 -d  176.58.123.25 -j REJECT 

打印当前生效的iptables规则（-n显示IP地址）
iptables -L -n 


Linux中iptables防火墙指定端口范围

 --dport 700:800 -j ACCEPT
一、 700:800  表示700到800之间的所有端口
二、 :800   表示800及以下所有端口
三、 700:   表示700以及以上所有端口


其他的iptables用途：
源地址转换(Snat): iptables -t nat -A -s 私IP -j Snat --to-source 公IP
目的地址转换(Dnat): iptables -t nat -A -PREROUTING -d 公IP -j Dnat --to-destination 私IP


iptables命令详解

当然可以通过man iptables来查看详细的解释。常用的命令选项有：

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



【例1】添加iptables规则禁止用户访问域名为www.sexy.com的网站。

iptables -I FORWARD -d www.sexy.com -j DROP
【例2】添加iptables规则禁止用户访问IP地址为20.20.20.20的网站。

iptables -I FORWARD -d 20.20.20.20 -j DROP


禁止某些客户机上网

【例1】添加iptables规则禁止IP地址为192.168.1.X的客户机上网。

iptables -I FORWARD -s 192.168.1.X -j DROP
【例2】添加iptables规则禁止192.168.1.0子网里所有的客户机上网。

iptables -I FORWARD -s 192.168.1.0/24 -j DROP


禁止客户机访问某些服务

【例1】禁止192.168.1.0子网里所有的客户机使用FTP协议下载。

iptables -I FORWARD -s 192.168.1.0/24 -p tcp –dport 21 -j DROP
【例2】禁止192.168.1.0子网里所有的客户机使用Telnet协议连接远程计算机。

iptables -I FORWARD -s 192.168.1.0/24 -p tcp –dport 23 -j DROP


强制访问指定的站点

【例】强制所有的客户机访问192.168.1.x这台Web服务器。

iptables -t nat -I PREROUTING -i eth0 -p tcp –dport 80 -j DNAT –to-destination 192.168.1.x:80


禁止使用ICMP协议

【例】禁止Internet上的计算机通过ICMP协议ping到NAT服务器的ppp0接口，但允许内网的客户机通过ICMP协议ping的计算机。

iptables -I INPUT -i ppp0 -p icmp -j DROP


发布内部网络服务器

【例1】发布内网10.0.0.3主机的Web服务，Internet用户通过访问防火墙的IP地址即可访问该主机的Web服务。

iptables -t nat -I PREROUTING -p tcp –dport 80 -j DNAT –to-destination 10.0.0.3:80
【例2】发布内网10.0.0.3主机的终端服务（使用的是TCP协议的3389端口），Internet用户通过访问防火墙的IP地址访问该机的终端服务。

iptables -t nat -I PREROUTING -p tcp –dport 3389 -j DNAT –to-destination 10.0.0.3:3389