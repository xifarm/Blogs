Ins老王网赚知识总结(1): URL传参(含Postback)、Offer cloked事件

最近网赚进展迅速，几个知识点测试通过了，真正的Media Buy了。 
本文就遇到的几个比较绕的知识点进行梳理，和大家分享。

#知识点1： URL传参与Postback
URL参数传递分形参和实参。

* 形参对应Parameter: 接收方定义和解析
* 实参对应Token： 发送方定义和实例化
* 
eg, https://www.xifarm.com/affid=111&offID=222&clickID={CID}

clickID就是接收方解析的形参，{CID}就是发送方实例化数据的实参


上述图中，我们得知共有7条路径，即需要7个URL。在这7个URL中，必须传的参数有3个:
* AffID: 网盟分配给你的ID
* offerID: 推广Offer的ID，如果是Smartlink，则这个ID可能有多个
* ClickID: 流量平台分配的本次点击ID，方便后续优化GEO、WebSite用。

流量路径： 1.1, 1.2, 1.3, 1.4 (如无LP，则1.2和1.3可以节省)
回传路径： 2.1， 2.1， 2.3 (2.1是必须的，流量如果不是CPM而是SmartCPA，则需要2.2和2.3)


流量路径展开细说：
- 1.1 Campaign URL:  Traffic --> Tracking 
4个必备参数：affID、offerID、clickID、cost
其他的如Wifi、Android、Country这些常用参数，我理解的Tracking系统可以通过解析Http Request Header的UserAgent获得。

- 1.2 LP URL: Tracking --> LP
  这里不需要传参，而且多个Campaign之间LP URL可以复用。
- 1.3 Click URL: LP --> Affiliate offer
  神奇的事情，同一个Tracking对应的Click URL是一样的，如http:/www.xifarm.com/click， 具体代表那个offer，则由Flow规则而定。 通过在Windows下用fiddler抓包，如下js代码就是user点击Click URL后，Tracking返回的，可以指定调用对应的Offer URL。

```
<html>
<head>
<link rel="icon" type="image/gif" href="data:image/gif;base64,R0lGODlhAQABAPAAAAAAAAAAACH5BAEAAAAALAAAAAABAAEAAAICRAEAOw=="/>
<meta http-equiv="refresh" content="0;URL='https://www.cpagrip.com/show.php?l=0&u=186548&id=22884&tracking_id=dRL2LTTFGQ4NSH0LHCBP4PA0'" />
</head>

<body>
<script>
window.setTimeout(function(){window.location.replace('https://www.cpagrip.com/show.php?l=0&u=186548&id=22884&tracking_id=dRL2LTTFGQ4NSH0LHCBP4PA0');}, 0);</script>
</body>
</html>
```
- 1.4 Offer URL: Tracking --> Affiliate offer
  三个必备参数：affID、offerID、clickID。
  Offer URL对应的Offer可以是Smartlink，也可以是Special Offer链接。 


回传路径展开细说：
- 2.1 Tracking Postback S2S:  Affiliate --> Tracking
很关键，发生了Lead后才调用，对在Tracking中决策很有用，横向对比。
需要2个参数:clickID、payout。
clickID就是传递过去的参数，payout就是本次转化产生的收益
常说的Postback，主要就是这个postback路径，一般在Affiliate的Global里面设置即可，后面没有换Tracking，就不用再修改了。也有一些特殊的情况，如A4G这个网盟，是需要通过和AM沟通，人工方式修改Postback的，修改了你也不知道，的确吐血。

一般Lead可能会延时30~60分钟，当然具体的以Affiliate 平台数据为准。

- 2.2 Trafic Postback S2S: Tracking --> Trafic
可选。
Traffic的大部分bid是CPM类型的，所以是否Lead和Traffic一毛钱关系都没有。有一种除外，就是SmartCPA这种Traffic是要求配置PostBack的。

- 2.3 Trafic Postback S2S: Affiliate --> Trafic
可选。
一般在Tracking中添加Traffic的时候，会自动配置，选择对应Traffic的模板即可。

Postback不仅仅是为了数据优化用，另外，很关键的是在于最大盈利化，如CAP这个不同Offer不同，大部分是共享的CAP,如Offer A日CPA就30个，那么配置后Postback后，启用Tracking的Rule，自动进行分流到其他Offer中，这样流量就浪费了。
你买流量有日Budget，同样的，广告主也是同理心，它也有日Budget预算。
另外，https是大势所趋，上面说的各个URL，都需要Https化，这个是免费的。

#知识点2： URL Cloaked知识点

Cloaked的原理是防止***， hat SEO的规则。
如期望好的流量进入URL A， 不是期望范围内的流量进入URL B。

Media Buy, 我们自然希望导入流量100%不要被Cloaked。

上周犯了一个错误，损失了几十刀，流量100%的被Cloaked，良心的是Afflow(Monetizer)这个老牌英国的网盟还可以知道被Cloaked了，所以开始查原因，初步诊断Afflow的自定义域名我是用CloudFlare做DNS的，在FAQ中关于Cloaked的提示。

避免类似问题的办法：
阅读文档：接触一个traffic、affiliate，磨刀不误砍柴工，需要先用几个小时看一下文档，至少FAQ要细细过一遍的，避免无谓的浪费。
手工EMU：建立一个Campaign，务必要手工EMU一个Lead出来，模拟GEO、Phone Type、WIFI，研读一下数据，然后再买量，做到心中有数。

同时，也可以顺道测试一下 #域名报毒#，域名出现问题，先Stop，抓紧时间换新的。

#知识点3： Tracking的盲从
刚开始，我选择是Bemob，其界面简洁大方、网速飞快，尤其适用在国内的网络情况。
经常性看到群里对Voluum有人吹水，不免的也口水连连，刚好前几天Voluum从299降价到69，就入手试用了一下，首先是费劲网络IP proxy，才正常可以用。诚然，Voluum的节目的确很漂亮，但是其价格竟然不包含自定义域名的HTTPS解析，需要HTTPS解析的还是需要299刀。而ZeroPark已经强制要求域名https了(估计2019年中期大部分都会要求https的)，而且在使用过程中，我这里的网络经常掉线，故而又换回Bemob了：提高效率、又省钱。

从Voluum这个事情上，再次证明Affiliate是个有趣的行业，它适合有学习、分享意愿的从业，但是不要盲从，选择适合自己的，Tracking的核心是数据中心，流量数据从这里过、Postback数据也在这集合，多个Campaign可以横向比较offer、traffic，在刚起步的早期，数据量很小(如一个月30万流量)，选择哪个平台都差不多，网络中无非用的Http get、post方式传数据，各家不太可能有太大差距(网络时延和点损)。 因为，你面对的是海量的互联网数据，几百刀也仅仅是“管中窥豹”而已。

