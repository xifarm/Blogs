## 目标
技术博客[www.xifarm.com](www.xifarm.com)有5年时间了.

原来在虚拟机/VPS上搭建，不过都是Windows系统下的。 最近突发奇想，试试迁移到Linux的Unbuntu下。说干就干，抽空用了大约3天时间*每天1~2小时投入，完成搭建。

这里记录一下过程，分享给有需求的朋友。

##1. **安装LAMP (Install Linux, Apache, MySQL, PHP)**
首先需要安装 (LAMP) stack, 顺序执行命令，很简单。

```
sudo apt update
sudo apt install apache2

http://your_server_ip

sudo apt install mysql-server
sudo mysql_secure_installation
sudo apt install php libapache2-mod-php php-mysql
sudo vi /etc/apache2/mods-enabled/dir.conf
```

新的wordPress已经推荐支持Php 7.2,所以直接安装php 7.2。
```
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install -y php7.2
```

##2. **安装WordPress**
```
cd /tmp
curl -O https://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz

touch /tmp/wordpress/.htaccess
cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.php

mkdir /tmp/wordpress/wp-content/upgrade
sudo cp -a /tmp/wordpress/. /var/www/wordpress
sudo chown -R www-data:www-data /var/www/wordpress
```

配置3个字段，可完成DB创建。（前置条件，需要先用MySQL创建DB，这里不再赘述）

```
curl -s https://api.wordpress.org/secret-key/1.1/salt/
```

vi 打开配置文件 /var/www/wordpress/wp-config.php

```


define('DB_NAME', 'wordpress');
/** MySQL database username */
define('DB_USER', 'wordpressuser');
/** MySQL database password */
define('DB_PASSWORD', 'password');

define('FS_METHOD', 'direct');
```

##3. **SSL增加HTTPs**
今年Google Chorome、Apple Safari先后声明，全力支持HTTPS，故，本次也一并把HTTPS考虑在内了。 原来以为HTTPs和域名一样需要申请、审核，比较费时。 

看了几篇博客，才知道通过certbot 申请，整个过程完全自动化，网速快的话10分钟可以完成申请（90天免费，写个自动化调度执行命令Refresh即可实现长期免费）。

按照顺序执行command：
```
sudo add-apt-repository ppa:certbot/certbot

sudo apt-get update
sudo apt-get install python-certbot-apache
sudo certbot --apache -d example.com
sudo certbot --apache -d example.com -d www.example.com
https://www.ssllabs.com/ssltest/analyze.html?d=example.com&latest

cron script placed in /etc/cron.d
sudo certbot renew --dry-run
```

##tips: 几个比较耗时的过程总结
##### **mySQL外网运维权限**
linux的安全大门果然紧闭，难怪安全呢；不像windows安全大门比较松，使用起来灵活一些。如，MySQL访问权限，Linux默认在localHost内开放，但是我们为了运维方便，需要向定向IP进行授权。

通过修改配置文件，可开启MySQL外网访问权限：增删改查。

打开/etc/mysql/mysql.conf.d/mysqld.cnf 文件

```
修改IP地址
bind-address       = 0.0.0.0

GRANT ALL ON *.* TO 'root'@'192.168.0.7' IDENTIFIED BY 'password' WITH GRANT OPTION;

FLUSH PRIVILEGES; 
EXIT;
GRANT ALL ON *.* TO 'user'@'localhost' IDENTIFIED BY 'passwd' WITH GRANT OPTION;
```

同时，需要Linux系统防火墙开放3306端口：
```
sudo ufw allow 3306
sudo ufw status
netstat -an | grep 3306
netstat -an | grep -i established
```

##### WordPress调试开关
导入备份的DB，打开博客首页，奇怪，没反应。

估计是DB或者配置问题，为了Debug log，需要开启了WordPress自带的log开关，查debug.log看看端倪。

在 wp-config.php，添加如下代码：
```
 // Enable WP_DEBUG mode
define( 'WP_DEBUG', true );

// Enable Debug logging to the /wp-content/debug.log file
define( 'WP_DEBUG_LOG', true );

// Disable display of errors and warnings 
define( 'WP_DEBUG_DISPLAY', false );
@ini_set( 'display_errors', 0 );

// Use dev versions of core JS and CSS files (only needed if you are modifying these core files)
define( 'SCRIPT_DEBUG', true );

```

##### WordPress固定连接访问失效解决
  为了SEO，我把默认的WordPress博客链接修改为 <http://www.xifarm.com/photononazure/>
  但是默认的Apache2没有打开这个规则，需要修改Apache的config文件，并重启Apache2服务。

```
vi /etc/apache2/apache2.conf

<Directory /path/to/wordpress>
  Options Indexes FollowSymLinks
  AllowOverride All
  Require all granted
</Directory>

sudo service apache2 restart
```

## 参考文章：
##### 1. [How To Install WordPress with LAMP on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lamp-on-ubuntu-18-04)

##### 2. [ How To Secure Apache with Let's Encrypt on Ubuntu 14.04](https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-14-04)

#####  3. [How to set up MySQL for remote access on Ubuntu Server 16.04](https://www.techrepublic.com/article/how-to-set-up-mysql-for-remote-access-on-ubuntu-server-16-04/)