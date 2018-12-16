
网络访问，使用IP Proxy，有一个很重要需求，隐藏原IP地址。

如果不隐藏，显示如下：
X-Forwarded-For: 191.1.2.5

如果因此，则显示如下：
X-Forwarded-For: unknown

查查已有的配置信息：
```
egrep -v '#|^ *$' /etc/squid3/squid.conf  #查配置信息
```

修改方法：
```
# vi /etc/squid3/squid.conf
```

我的squid.conf配置信息如下：
```
# Hide client ip #
forwarded_for delete
 
# Turn off via header #
via off
 
# Deny request for original source of a request
follow_x_forwarded_for deny all
``` 


重启服务
```
# /etc/init.d/squid restart
```


IP信息测试网址：
https://www.ip-adress.com/what-is-my-ip-address