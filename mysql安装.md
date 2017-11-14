title: MySQL安装  
tags: [MySQL,linux]  
date: 2017-11-11  

---

### MySQL安装步骤 ###

1. 彻底删除MySQL
```
yum remove mysql mysql-server mysql-libs compat-mysql51
rm -rf /var/lib/mysql
rm /etc/my.cnf
```
2. 查看是否还有mysql软件,有的话继续删除  
```
rpm -qa|grep mysql  
```
3. 注意：安装前先确认系统时区及系统时间是否正确  
在 [mysqld] 之下加default-time-zone='+8:00' 来修正MySQL时区问题  

4.去网站下载mysql的yum源，地址如下：  
http://repo.mysql.com/  
在linux上先查看系统的版本号，根据版本号对应下载 (此处请注意，不明白的去查资料，不要乱下载)  
```
rpm -Uvh http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm  
yum -y upgrade mysql  
mysql_upgrade  
yum install mysql-server  
service mysqld restart  
```
日志文件：/var/log/mysqld.log
