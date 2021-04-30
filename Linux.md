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

#### ifconfig不显示ip问题

```bash
# 进入到指定目录
cd /etc/sysconfig/network-scripts/
# 查看名字
ls
ifcfg-ens33 # 可能事其他名字
# 编辑配置文件
vi ifcfg-ens33
# 具体修改
ONBOOT=no,将no  改为yes
```

### CentOS7.x 搭建FTP服务器,Java上传下载

```bash
# 1.查看系统是否自带vsftpd软件
rpm -qa | grep vsftpd

# 2.使用yum安装vsftpd软件
yum install vsftpd -y

# 3.启动服务，并查看21端口是否处于监听状态
systemctl start vsftpd
netstat -nltp | grep 21

# 4.停止服务和重启服务的命令
systemctl stop vsftpd.service
systemctl restart vsftpd.service

# 5.为FTP创建用户，并为用户设置密码，以及用户主目录
useradd ftpuser  #创建用户
echo "ftpuser" | passwd ftpuser --stdin  #为用户设置密码
usermod -s /sbin/nologin ftpuser  #限制该用户只能访问ftp服务器

# 6.为用户设置主目录
mkdir -p /data/ftp/pub  #创建根目录
chmod a-w /data/ftp && chmod 777 -R /data/ftp/pub  #设置访问权限
usermod -d /data/ftp ftpuser  #设置为用户的主目录： 即用户通过 FTP 登录后看到的根目录

# 7.访问ftp服务
ftp://ftpuser:123456@101.37.174.111
# 8.注意防火墙、开放端口等

# 9.修改SETLinux权限
getenforce  #查看修改前的结果
setenforce 0  #修改

# 配置文件备份
mv vsftpd.conf vsftpd.conf_bak #重命名
cat vsftpd.conf_bak | grep -v "#" # 过滤掉带#的内容
cat vsftpd.conf_bak | grep -v "#" > vsftpd.conf # 输出过滤后的内容

# 匿名登录配置
anon_umask=022
anon_upload_enable=YES #允许上传
anon_mkdir_write_enable=YES #允许写入
anon_other_write_enable=YES #允许其他操作

# ftp服务登录报530
#vsftpd默认会检查用户的shell，如果用户的shell在/etc/shells没有记录，则无法登陆ftp
/sbin/nologin
```

1.userlist_enable和userlist_deny两个选项联合起来针对的是：本地全体用户（除去ftpusers中的用户）和出现在user_list文件中的用户以及不在在user_list文件中的用户这三类用户集合进行的设置。

2.当且仅当userlist_enable=YES时：userlist_deny项的配置才有效，user_list文件才会被使用；当其为NO时，无论userlist_deny项为何值都是无效的，本地全体用户（除去ftpusers中的用户）都可以登入FTP

3.当userlist_enable=YES时，userlist_deny=YES时：user_list是一个黑名单，即：所有出现在名单中的用户都会被拒绝登入；

4.当userlist_enable=YES时，userlist_deny=NO时：user_list是一个白名单，即：只有出现在名单中的用户才会被准许登入(user_list之外的用户都被拒绝登入)；另外需要特别提醒的是：使用白名单后，匿名用户将无法登入！除非显式在user_list中加入一行：anonymous

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

#### 读写分离

##### **pom.xml**

```xml
<dependencies>
        <!--依赖web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--依赖mybatis和mysql驱动-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.4</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <!--依赖lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!--依赖sharding-->
        <dependency>
            <groupId>org.apache.shardingsphere</groupId>
            <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
            <version>4.0.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shardingsphere</groupId>
            <artifactId>sharding-core-common</artifactId>
            <version>4.0.0</version>
        </dependency>
        <!--依赖数据源druid-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.22</version>
        </dependency>
</dependencies>
```

##### yml

```yml
server:
  port: 8086
spring:
  main:
    allow-bean-definition-overriding: true
  shardingsphere:
    #参数配置显示sql
    props:
      sql:
        show: true
    #配置数据源
    datasource:
      #给每个数据源取别名
      names: ds1,ds2,ds3
      ds1:
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://192.168.150.128:3306/sharding_jdbc?useSSL=false&Unicode=true&characterEncoding=utf-8&serverTimezone=Asia/Shanghai
        username: root
        password: 123456
        maxPoolSize: 100
        minPoolSize: 5
      ds2:
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://192.168.150.129:3306/sharding_jdbc?useSSL=false&Unicode=true&characterEncoding=utf-8&serverTimezone=Asia/Shanghai
        username: root
        password: 123456
        maxPoolSize: 100
        minPoolSize: 5
      ds3:
        type: com.alibaba.druid.pool.DruidDataSource
        driver-class-name: com.mysql.cj.jdbc.Driver
        url: jdbc:mysql://192.168.150.129:3306/sharding_jdbc?useSSL=false&Unicode=true&characterEncoding=utf-8&serverTimezone=Asia/Shanghai
        username: root
        password: 123456
        maxPoolSize: 100
        minPoolSize: 5
    #配置默认数据源ds1
    sharding:
      # 注意要配置读写分离
      default-data-source-name: ds1
    #
    masterslave:
      #配置主从名字随便取
      name: ms
      #配置master主库
      master-data-source-name: ds1
      #配置slave从库
      slave-data-source-names: ds2,ds3
      #配置slave负载均衡规则 轮询
      load-balance-algorithm-type: round_robin

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.learn.entity
```

#### 分库分表

##### 水平拆分

##### 垂直拆分

#### sharding-jdbc事务

```java
@Transactional(rollbackFor = Exception.class)//出现异常回滚，在单库里面可以解决
@ShardingTransactionType(TransactionType.XA)//分布式事务，需要导包
public int save(){
    方法1
    方法2
}
```

