

树莓派安装WordPress，搭建LAMP服务
Linux, Apache, MySql, PHP


Raspbian  --  Debian （Linux kernel 4.19.57）



执行命令：
```

apt-get install apache2

```
## 使用 Etcher 给 SD 卡安装树莓派系统

在 Windows 下给树莓派安装系统可以使用 win32diskimager，下面介绍的是 Etcher，它不仅支持 Windows 下使用，在 macOS 下以及 Linux 系统下也能找到相应的版本。而且使用更加简化，仅需三步。

http://shumeipai.nxez.com/2019/04/17/write-pi-sd-card-image-using-etcher-on-windows-linux-mac.html



## 初次使用树莓派并启用root管理员（登录root管理员）
初次使用树莓派系统时，默认用户是pi ，密码为raspberry。

要想使用root帐号，或者说开启root用户，可使用pi用户登录，执行下面命令（此命令是给root账户设置密码的，当切换到root管理员后，此命令无效）


sudo passwd root
说明：sudo是linux系统管理指令，是允许系统管理员让普通用户执行一些或者全部的root命令的一个工具，如halt，reboot，su等等

执行此命令后系统会提示输入两遍的root密码（用来确保你记住了密码）。

接着输入下面命令，用来解锁root账户

sudo passwd --unlock root
用下面命令切换到root管理员
su root
会提示输入密码
当从root用户切换到pi用户后

## 安装MySQL报错：Package 'mysql-server' has no installation candidate
root@raspberrypi:/var/www/html# apt install mysql-server php-mysql 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Package mysql-server is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source
However the following packages replace it:
  mariadb-server-10.0

E: Package 'mysql-server' has no installation candidate