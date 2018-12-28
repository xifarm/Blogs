
上个月，通过Unbuntu搭建了WordPress，一切运行良好。
[UBUNTU搭建WORDPRESS-MYSQL-APACHE](http://www.xifarm.com/ubuntuwordpress-mysql-apache/)

但是，最近几天，不知道啥情况，MySQL偶尔会出现Stop；影响了blog的使用，所以，我这里尝试了自动调度，间隔1分钟查看MySQL，如果Stop，则自动重启。

在网上找到对应的解决方案，分3步实施。
Bash Script to check if services are running and restart if not. Sends email to you.
[sierracircle/services-checker](https://github.com/sierracircle/services-checker)

### Step1：配置脚本

```
/scripts/services.sh
```
拷贝上面的github源码，修改邮箱和你需要启动的服务。

```
chmod +x services.sh 
```

测试shell脚本
```
./services.sh
bash services.sh
```

### Step2： 配置crontab 守护进程
crond是linux下用来周期性的执行某种任务或等待处理某些事件的一个守护进程，与windows下的计划任务类似，当安装完成操作系统后，默认会安装此服务工具，并且会自动启动crond进程，crond进程每分钟会定期检查是否有要执行的任务，如果有要执行的任务，则自动执行该任务。

```
crontab -e

#check on services
*/1 *  * * * /your/path/to/scripts/services
```



### Step3：启动守护进程
大约需要2分钟。
你可以尝试手工Stop MySQL，1分钟后观察结果。

```
service mysql stop
```

### 参考crontab使用实例。

```
实例1：每1分钟执行一次command
命令：
* * * * * command

实例2：每小时的第3和第15分钟执行
命令：
3,15 * * * * command

实例3：在上午8点到11点的第3和第15分钟执行
命令：
3,15 8-11 * * * command

实例4：每隔两天的上午8点到11点的第3和第15分钟执行
命令：
3,15 8-11 */2 * * command

实例5：每个星期一的上午8点到11点的第3和第15分钟执行
命令：
3,15 8-11 * * 1 command

实例6：每晚的21:30重启smb 
命令：
30 21 * * * /etc/init.d/smb restart

实例7：每月1、10、22日的4 : 45重启smb 
命令：
45 4 1,10,22 * * /etc/init.d/smb restart

实例8：每周六、周日的1 : 10重启smb
命令：
10 1 * * 6,0 /etc/init.d/smb restart

 

实例9：每天18 : 00至23 : 00之间每隔30分钟重启smb 
命令：
0,30 18-23 * * * /etc/init.d/smb restart

实例10：每星期六的晚上11 : 00 pm重启smb 
命令：
0 23 * * 6 /etc/init.d/smb restart

实例11：每一小时重启smb 
命令：
* */1 * * * /etc/init.d/smb restart
 
实例12：晚上11点到早上7点之间，每隔一小时重启smb 
命令：
* 23-7/1 * * * /etc/init.d/smb restart

实例13：每月的4号与每周一到周三的11点重启smb 
命令：
0 11 4 * mon-wed /etc/init.d/smb restart
 
实例14：一月一号的4点重启smb 
命令：
0 4 1 jan * /etc/init.d/smb restart

实例15：每小时执行/etc/cron.hourly目录内的脚本
命令：
01   *   *   *   *     root run-parts /etc/cron.hourly
```