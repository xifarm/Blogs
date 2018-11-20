完整的MAC搭建私链，参考如下三篇博客即可，再次不再赘述。


[区块链学习一 MAC上以太坊私有链搭建](https://www.jianshu.com/p/cd5aed9b06af)

[快速搭建以太坊私链](https://www.jianshu.com/p/d2d21ff15c89)

[以太坊开发极简入门](https://www.jianshu.com/p/bec173e6cf73)



这里写的一些坑的地方，MAC系统下。


### 1. Mac下打开/usr/local目录
Mac下/usr/local目录默认是对于Finder是隐藏，如果需要到/usr/local下去，打开Finder，然后使用command+shift+G，在弹出的目录中填写/usr/local就可以了。


### 2. Brew安装问题(Homebrew)
安装 Homebrew 很简单，但是速度太慢了，我反复试了6次，fq、手机热点等等，才成功。

[安装 Homebrew  官网 ](https://brew.sh/index_zh-cn)


### 3. chown: /usr/local: Operation not permitted问题解决

在MAC上安装homebrew，根据步骤进行下去，在brew update的时候出现报错：Error: /usr/local must be writable! 错误.

解决办法（sudo chown -R $(whoami) /usr/local） 对 高版本的OS来说，是解决不了的，会报 chown: /usr/local: Operation not permitted错误。



先卸载已安装的homebrew，命令如下：

> /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"

然后重新安装：
> /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

通过该方法直接获取最新的homebrew。




### 4. mac 执行brew update 报错 Error: Could not link: /usr/local/etc/bash_completion.d/brew

> Error: Could not link:
> /usr/local/share/doc/homebrew
> 
> Please delete these paths and run `brew update`.


解决办法

> rm -rf /usr/local/share/doc/homebrew
> brew update



### 5. brew install xxx卡在Updating Homebrew

解决方法，关闭自动更新
> export HOMEBREW_NO_AUTO_UPDATE=true


### 6. macbook 启用tab自动补全
要配置tab自动补全功能，得需要用root用户权限，那么就先启动root权限.

打开终端之后，现在默认进去的是你登录的用户，现在要切换成root用户：

1、su root，然后输入密码成功登录

2、输入 nano .inputrc

3、再输入以下语句：

> set completion-ignore-case on
> 
> set show-all-if-ambiguous on

4、输入完毕后，按 Control ＋ o 
.关闭当前终端，重新打开一个新的终端，tab补全不区分大小写，成功。



miner.stop()之后回车，即可停止挖矿。
停不住。。。

> INFO [07-07|19:00:21.438] Generating DAG in progress               epoch=1 percentage=29 elapsed=26.360s
> INFO [07-07|19:00:22.325] Generating DAG in progress               epoch=1 percentage=30 elapsed=27.247s



MAC 发烫啊。








