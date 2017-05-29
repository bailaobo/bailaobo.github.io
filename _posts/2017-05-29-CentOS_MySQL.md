---
layout: post
title: CentOS6.5安装MySQL5.5.x
author: BaiLaobo
tags:
  - MySQL
  - CentOs
categories: DB
date: 2017-05-29 12:12:00
---
centos6.5 安装 mysql5.5.x
-------------
```bash
添加主机名和IP的映射 修改 /etc/hosts 
vim /etc/hosts　
#添加一条 IP HostName
192.168.31.122 T02
```
1. 查询旧版mysql包
```
[root@T02 pkg]# rpm -qa|grep mysql
mysql-libs-5.1.71-1.el6.x86_64
```



2. 卸载mysql-libs包
```
[root@T02 pkg]# rpm -ev mysql-libs-5.1.71-1.el6.x86_64 --nodeps
```
3. 安装msyql server,client,devel 三个包
    *.rpm 当文件夹 只有这3个包可以这样用否则请枚举文件名
```
[root@T02 pkg]# rpm -ivh *.rpm | tee  mysql_install.log
```
4. 创建mysql数据目录 修改文件夹属组
```
mkdir -p /data/mysql 
chown mysql:mysql /data/mysql
```
5. 拷贝my.cnf，根据不同硬件配置选择不同的my-*.cnf
```bash
cp /usr/share/mysql/my-medium.cnf /etc/my.cnf
#修改my.cnf配置 修改数据库路径
[client]
socket          = /data/mysql/mysql.sock
[mysqld]
datadir = /data/mysql
socket          = /data/mysql/mysql.sock
#log-error  = /data/mysql/error.log
#pid-file = /data/mysql/mysql.pid
#关闭区分大小写
lower_case_table_names=1
```
6. 安装数据实例使用自定义数据目录
```bash
/usr/bin/mysql_install_db  --datadir=/data/mysql --user=mysql 
```
7. 启动数据库

```text
  启动数据库需要 关闭selinux 
  #setenforce 0 #临时关闭selinux 不需要重新启动
  /etc/selinux/config  文件
  找到SELINUX=enforcing修改为SELINUX=disabled
  #service mysql start 
  #mysql  #进入mysql客户端  查看socket目录
  mysql> select @@socket;
  +------------------------+
  | @@socket               |
  +------------------------+
  | /data/mysql/mysql.sock |
  +------------------------+

```
8. MySQL安全配置向导
```bash
/usr/bin/mysqladmin -u root password '123456'
# 如果未设置 IP和主机名称的映射，改行会宝座，造成后面user表 t02(后又改成%) 没有密码字段没有值
/usr/bin/mysqladmin -u root -h T02 password '123456'
# mysql_secure_installation 脚本需要这个socket路径 暂时做软连接 配置后可删除该符号连接
ln -s /data/mysql/mysql.sock /var/lib/mysql/mysql.sock
# 开始 安全配置向导
/usr/bin/mysql_secure_installation 
Enter current password for root (enter for none): #123456
Change the root password? [Y/n] #是否变更root密码 n 这个看需要
Remove anonymous users? [Y/n] #删除匿名账号 Y
Disallow root login remotely? [Y/n]  #禁用root远程登录 n 
Remove test database and access to it? [Y/n] #删除test数据库 Y
Reload privilege tables now? [Y/n] #立即重载权限表 Y  
```
9. 设置root远程连接
```bash
 #关闭防火墙 或者 开放 数据库端口 （3306）
 #查看防火墙状态
 service iptables status
 #编辑文件参照22端口 开放3306端口
 vim /etc/sysconfig/iptables
 -A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
 #重启防火墙服务
  service iptables restart
```
```sql

 mysql -uroot -p123456
 mysql> select host,user from mysql.user; 
+-----------+------+
| host      | user |
+-----------+------+
| 127.0.0.1 | root |
| ::1       | root |
| localhost | root |
| t02       | root |
+-----------+------+
mysql>update  mysql.user set host='%' where host='t02' and user = 'root';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0
mysql> select host,user from mysql.user; /*在查看一下*/
mysql> flush privileges; /*刷新MySQL的系统权限相关表*/
/*
若还有问题重启一下mysql服务看
service mysql restart
*/
```
10.注意事项
```
root权限过高应该禁止远程连接，建立新的低权限账号来管理数据库。
```