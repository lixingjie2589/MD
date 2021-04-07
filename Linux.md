### Linux

#### 防火墙

#查看防火墙规则
firewall-cmd --list-all
firewall-cmd --list-ports
#开启端口
firewall-cmd --zone=public --add-port=9000/tcp --permanent
#重启防火墙
systemctl restart firewalld.service

#### 安装vim

```bash
yum -y install vim*
```



### VMware

配置参考

https://blog.csdn.net/qq_38289539/article/details/74394168

记得配置iso镜像

安装操作系统参考

https://www.runoob.com/w3cnote/vmware-install-centos7.html

https://baijiahao.baidu.com/s?id=1658940916954646386&wfr=spider&for=pc

### Mysql

#### 安装

**下载页面**

``` bash
http://repo.mysql.com/yum/mysql-5.7-community/el/7/x86_64/
```
**配置Mysql扩展源**

``` bash
rpm -ivh http://repo.mysql.com/yum/mysql-5.7-community/el/7/x86_64/mysql57-community-release-el7-10.noarch.rpm
```
**安装MySQL服务器**

``` bash
yum -y install mysql-community-server
```
**启动Mysql**

```bash
systemctl start mysqld #启动
systemctl status mysqld #查看启动状态
systemctl stop mysqld
systemctl enable mysqld
```

**进行密码配置**

```bash
# 查看密码
grep "password" /var/log/mysqld.log
# 登录
mysql -uroot -p
# 或者一步到位
mysql -uroot -p$(awk '/temporary password/{print $NF}' /var/log/mysqld.log)
# 修改密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
# 需要设置一下密码等级
set global validate_password_policy=0;or LOW
# 设置密码长度限制
set global validate_password_length=6;
# 如下命令查看mysql默认密码复杂度
SHOW VARIABLES LIKE 'validate_password%';
# 开启mysql的远程访问
grant all privileges on *.* to 'root'@'192.168.0.1' identified by 'password' with grant option; # 如要开启所有的，用%代替IP
# 刷新
flush privileges;
# 重启服务
service mysqld restart
# 以上参考
https://www.cnblogs.com/yss818824/p/12349719.html
```

#### 主从复制

##### 具体配置

> Master节点配置 /etc/my.cnf (master节点执行)

```bash
[root@localhost /]# vim /etc/my.cnf

[mysqld]
## 同一局域网内要唯一
server-id=100
## 开启二进制日志功能，可以随便取
log-bin=mysql-bin
## 复制过滤：不需要备份的数据库（mysql库一般不同步）
binlog-ignore-db=mysql
## 为每个session分配的内存，在事务过程中用来存储二进制日志的缓存
binlog_cache_size=1M
## 主从复制的格式( mixed,statement,row.默认格式是statement )
binlog_format=mixed
```

> Slave节点配置 /etc/my.cnf (slave节点执行)

```bash
[root@localhost /]# vim /etc/my.cnf

[mysqld]
## 同一局域网内要唯一
server-id=101
## 开启二进制日志功能，可以随便取
log-bin=mysql-slave-bin
## relay_1og配置中继日志
relay_log=edu-mysql-relay-bin
## 复制过滤：不需要备份的数据库（mysql库一般不同步）
binlog-ignore-db=mysql
## 如果需要同步函数或者存储过程
log_bin_trust_function_creators=true
## 为每个session分配的内存，在事务过程中用来存储二进制日志的缓存
binlog_cache_size=1M
## 主从复制的格式( mixed,statement,row.默认格式是statement )
binlog_format=mixed
## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
## 如: 1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
```

> 在master服务器授权slave服务器可以同步权限(master节点执行)

```bash
#授予slave服务器可以同步master服务
mysql> grant replication slave, replication client on *.* to 'root'@'slave ip' identified by 'slave密码';
#查看MySQL现在有哪些用户及对应的IP权限(可以不执行，只是一个查看)
select user,host from mysql.user;
#查看二进制文件
show master status;
```

> slave进行关联master节点(slave节点执行)

```bash
# 执行命令进行绑定
mysql> change master to master_host='master服务ip', master_user='root', master_password='master密码', master_port=3306, master_log_file='二进制文件名', master_log_pos=二进制文件位置;

# 启动主从复制
start slave;
#查看
show slave status\G;#\G 换行
# 看到两YES表示成功
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```

**可能出现错误**

```bash
# 一直处于连接状态
Slave_IO_Running：Connecting
网络不通
检查ip,端口
密码不对
检查是否创建用于同步的用户和用户密码是否正确
pos不对
检查Master的Position

#MYSQL镜像服务器因错误停止的恢复一Slave_SQL_Running: No
> stop slave;
> set global sql_ slave_ skip_ counter=1;
> startl slave;
> show slave status\G;

# 从MYSQL服务器Slave_I0_Running: No的解决2
master查看二进制文件，slave重新绑定一下
```

