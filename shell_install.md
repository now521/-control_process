# 1、安装iostat命令：rpm -ivh sysstat-9.0.4-31.el6.x86_64.rpm  
![image1](https://github.com/now521/control_picture/blob/master/%E5%9B%BE%E7%89%871.png)
# 2、安装jq命令：rpm -ivh jq-1.3-2.el6.x86_64.rpm  
![image2](https://github.com/now521/control_picture/blob/master/%E5%9B%BE%E7%89%872.png)
# 3、把脚本放到指定目录：/root/pre_process/下
tar xf xunjian.tar.gz;mv xunjian /root/pre_process/

# 4、如果是在其他客户端部署脚本，需要安装MySQL客户端：
rpm -ivh mysql-5.1.71-1.el6.x86_64.rpm
![image3](https://github.com/now521/control_picture/blob/master/%E5%9B%BE%E7%89%873.png)
用my.cnf替换到/etc/my.cnf  
服务端如果没有修改编码格式的话也要做相应的添加  
```
[mysqld]
default-character-set=utf8
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
sql_mode=STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
character-set-server=utf8

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

[client]
port=3306
socket=/var/lib/mysql/mysql.sock
default-character-set=utf8
```
# 5、编辑脚本文件：
```
cd /root/pre_process/xunjian/config
./syschk.sh

Usage: ./syschk.sh {sys_list|PING|firewall|sys|log|mysql|ftp|nifi|mfsmaster|mfsmetalogger|mfscgiserv|mfschunkserver|mfsclient|engine|quality|cesic|redis|cdh_nn|cdh_dn|solr|smartv|solr_interface|cdh_interface|redio}
#vim sys.ini
eth1
sys_list
firewall
sys
log
mysql
ftp
nifi
mfsmaster
mfsmetalogger
mfscgiserv
mfschunkserver
mfsclient
engine
quality
cesic
redis
cdh_nn
cdh_dn
solr
smartv
solr_interface
cdh_interface
redio
```
第一行写当前服务器的网卡名称，后面写本服务器要监控的监控项
```
sys_list：监控服务器列表。
PING：ping各个client服务器。这个只需要在server端的sys.ini填写
firewall：监控防火墙状态。
sys：监控服务器基本信息状态，包括cpu、内存、磁盘等。
log：监控日志                               mysql：监控mysql服务状态。
ftp：监控ftp服务状态。                      nifi：监控nifi服务状态。
mfsmaster：监控mfsmaster服务状态。
mfsmetalogger：监控mfsmetalogger服务状态。  mfscgiserv：监控mfscgiserv服务状态。
mfschunkserver：监控mfschunkserver服务状态。 mfsclient：监控mfsclient挂载状态。
engine：监控engine服务状态。                quality：监控quality服务状态。
cesic：监控cesic服务状态。                   redis：监控redis服务状态。
cdh_nn：监控nn节点cdh服务状态。           cdh_dn：监控dn节点cdh服务状态。
solr：监控solr服务状态。                      smartv：监控smartv服务状态。
solr_interface：监控solr服务接口状态。     cdh_interface：监控cdh服务接口状态。
redio：监控语音转译情况。
```
按照说明来填，一行一个对应。
# 6、修改index.sh和ck.sh脚本：（这个只需要在server端配置）
vim index.sh:将箭头指向的位置改成redis服务器ip地址
![image7](https://github.com/now521/control_picture/blob/master/%E5%9B%BE%E7%89%877.png)
vim ck.sh:红框里的字母代表机构领域，一个机构领域一行，代表一个机构领域执行一次。
![image8](https://github.com/now521/control_picture/blob/master/%E5%9B%BE%E7%89%878.png)
# 7、修改syschk.sh脚本：
vim syschk.sh（每一个变量都有相应的解释，按照解释修改即可）
![image9](https://github.com/now521/control_picture/blob/master/%E5%9B%BE%E7%89%879.png)
注意一定要检查一下mysql命令的配置：
![image10](https://github.com/now521/control_picture/blob/master/%E5%9B%BE%E7%89%8710.png)
# 8、将脚本添加到计划任务：
```
crontab -e
0 * * * * /bin/sh /root/pre_process/xunjian/ck.sh
0 0 * * * /bin/sh /root/pre_process/xunjian/day.sh
```
