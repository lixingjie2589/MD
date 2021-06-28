### Linux

#### 基本命令

```bash
uname -r # 查看系统内核
cat /etc/os-release # 系统版本
```



#### 防火墙

#查看防火墙规则
firewall-cmd --list-all
firewall-cmd --list-ports
#开启端口
firewall-cmd --zone=public --add-port=9000/tcp --permanent
#重启防火墙
systemctl restart firewalld.service

#### free命令

```bash
# 语法
free [-bkmotV][-s <间隔秒数>]
# 参数
-b 　以Byte为单位显示内存使用情况。
-k 　以KB为单位显示内存使用情况。
-m 　以MB为单位显示内存使用情况。
-h 　以合适的单位显示内存使用情况，最大为三位数，自动计算对应的单位值。
-o 　不显示缓冲区调节列。
-s <间隔秒数> 持续观察内存使用状况。
-c <次数> 打印次数
-t 　显示内存总和列。
-V 　显示版本信息。
```
**Mem，-/+ buffers/cache，Swap 解释**
| 名称              | 含义                           |
| ---------------- | ------------------------------ |
| Mem               | 内存的使用情况                 |
| -/+ buffers/cache | 表示物理内存已用多少，可用多少 |
| Swap              | 交换空间的使用情况             |
**total，used，free，shared，buffers，cached 解释**

| 名称    | 含义                                                         |
| ------- | ------------------------------------------------------------ |
| total   | 总量                                                         |
| used    | 已使用的                                                     |
| free    | 空闲的                                                       |
| shared  | 共享的，在linux里面有很多共享内存，比如一个libc库，很多程序调用，但实际只存一份 |
| buffers | 缓存，可回收                                                 |
| cached  | 缓存，可回收                                                 |





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

### FTP服务器

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

### GitLab



### nginx

**安装需要的组件**

```bash
#gcc && g++
yum install gcc-c++
#pcre
yum install -y pcre pcre-devel
#zlib
yum install -y zlib zlib-devel
#openssl
yum install -y openssl openssl-devel
```

**安装**

```bash
#解压nginx文件
tar -zxvf nginx-1.17.5.tar.gz
#安装
## 创建一个nginx安装目录
mkdir nginx
cd nginx-1.12.2
## 指定文件安装路径
./configure --prefix=/opt/nm/nginx
make
make install
#安装完成后内容会安装到指定的路径 /opt/nm/nginx下，否则会在默认目录/usr/local/nginx
```

**启动nginx**

```bash
## 修改配置文件
cd /opt/nm/nginx/conf
vim nginx.conf
## 设置端口为8080，也可设置成其他
listen    8080;
## 进入到启动目录
cd /opt/nm/nginx/sbin
## 检查配置文件是否有问题
./nginx -t
##没有问题的结果如下所示：
[soa@testsoa04 sbin]$ ./nginx -t
nginx: the configuration file /home/lege/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /home/lege/nginx/conf/nginx.conf test is successful
[soa@testsoa04 sbin]$ 
  
## 查询配置参数
./nginx -V
## 对于已安装的nginx需要修改配置参数
./configure --prefix=/home/lege/nginx ...配置参数
make
make install 
然后重新启动nginx即可
## 启动
./nginx
## 停止
./nginx -s stop
## 重启
./nginx -s reload
## 输入网址验证是否启动成功
http://ip:port/
```

https://www.jb51.net/article/174467.htm



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

#### 卸载

```bash
# 查看是否安装
rpm -qa | grep -i mysql
# 删除查出来的文件
rpm -e --nodeps MySQL-server-5.6.23-1.el6.x86_64
# 查看相关文件
find / -name mysql
# 删除(不删除不知道有无影响)
rm -rf $(find / -name mysql)
```

#### 通过 *.frm、*.ibd 文件恢复表结构数据

```bash
# .frm文件可以获取表结构
# .ibd文件是表空间(数据)

# 下载工具 此处使用dbsake
curl -s get.dbsake.net > dbsake
# 设置权限
chmod u+x dbsake
# 运行后得到创建表的语句
./dbsake  frmdump  [frm-file-path]
# 得到表结构后就是恢复数据
# 删除新建表的表空间
alter table  表名  discard tablespace;
# 把.ibd文件复制到相应位置下
# 修改拥有者
chown -R mysql.mysql *.ibd
# 将新的表空间与表结构进行绑定
alter table  表名  import tablespace;

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





### ElasticSearch

#### Docker安装Elasticsearch、Kibana

**1、下载镜像文件**

```bash
# 存储和检索数据
docker pull elasticsearch:7.4.2
# 可视化检索数据
docker pull kibana:7.4.2
```

**2、配置挂载数据文件夹**

```bash
# 创建配置文件目录
mkdir -p /mydata/elasticsearch/config
# 创建数据目录
mkdir -p /mydata/elasticsearch/data
# 将/mydata/elasticsearch/文件夹中文件都可读可写
chmod -R 777 /mydata/elasticsearch/
# 配置任意机器可以访问 elasticsearch
echo "http.host: 0.0.0.0" >/mydata/elasticsearch/config/elasticsearch.yml
```

3、启动Elasticsearch

```bash
docker run --name elasticsearch -p 9200:9200 -p 9300:9300 \
-e  "discovery.type=single-node" \
-e ES_JAVA_OPTS="-Xms64m -Xmx512m" \
-v /mydata/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
-v /mydata/elasticsearch/data:/usr/share/elasticsearch/data \
-v /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-d elasticsearch:7.4.2
```

- `-p 9200:9200 -p 9300:9300`：向外暴露两个端口，9200用于HTTP REST API请求，9300 ES 在分布式集群状态下 ES 之间的通信端口；

- `-e  "discovery.type=single-node"`：es 以单节点运行

- `-e ES_JAVA_OPTS="-Xms64m -Xmx512m"`：设置启动占用内存，不设置可能会占用当前系统所有内存

- -v：挂载容器中的配置文件、数据文件、插件数据到本机的文件夹；

- `-d elasticsearch:7.6.2`：指定要启动的镜像

访问 IP:9200 看到返回的 json 数据说明启动成功。

**4、设置 Elasticsearch 随Docker启动**

```bash
# 当前 Docker 开机自启，所以 ES 现在也是开机自启
docker update elasticsearch --restart=always
```

**5、启动可视化Kibana**

```bash
docker run --name kibana \
-e ELASTICSEARCH_HOSTS=http://192.168.35.131:9200 \
-p 5601:5601 \
-d kibana:7.4.2
```

`-e ELASTICSEARCH_HOSTS=http://192.168.163.131:9200`: **这里要设置成自己的虚拟机IP地址**

浏览器输入192.168.163.131:5601 测试

**6、设置 Kibana 随Docker启动**

```bash
# 当前 Docker 开机自启，所以 kibana 现在也是开机自启
docker update kibana --restart=always
```

#### Elasticsearch-使用入门

**_cat**

1. /_cat/nodes :  查看所有节点

2. /_cat/health：查看ES健康状况

3. /_cat/master：查看主节点信息

4. /_cat/indices：查看所有索引   等价于 mysql 数据库的 show databases;

**索引一个文档**

即保存一条数据，保存在哪个索引的哪个类型下，指定用哪个唯一标识。

1. PUT 请求

接口：`PUT http://192.168.163.131:9200/customer/external/1`

2. POST 请求

接口：`POST http://192.168.163.131:9200/customer/external/`

PUT和POST都可以

- POST新增，如果不指定id，会自动生成id。指定id就会修改这个数据，并新增版本号；
- PUT可以新增也可以修改。PUT必须指定id；由于PUT需要指定id，我们一般用来做修改操作，不指定id会报错。

**查看文档**

/index/type/id

接口：`GET http://192.168.163.131:9200/customer/external/1`

```js
{
    "_index": "customer",  # 在哪个索引(库)
    "_type": "external",   # 在哪个类型(表)
    "_id": "1",            # 文档id(记录)
    "_version": 5,         # 版本号
    "_seq_no": 4,          # 并发控制字段，每次更新都会+1，用来做乐观锁
    "_primary_term": 1,    # 同上，主分片重新分配，如重启，就会变化
    "found": true,
    "_source": {           # 数据
        "name": "zhangsan"
    }
}
# 乐观锁更新时携带 ?_seq_no=0&_primary_term=1  当携带数据与实际值不匹配时更新失败
```

**更新文档**

/index/type/id/_update

接口：`POST http://192.168.163.131:9200/customer/external/1/_update`

几种更新文档的区别:

在上面索引文档即保存文档的时候介绍，还有两种更新文档的方式：

- 当PUT请求带id，且有该id数据存在时，会更新文档；
- 当POST请求带id，与PUT相同，该id数据已经存在时，会更新文档；

这两种请求类似，即带id，且数据存在，就会执行更新操作。

类比：

- 请求体的报文格式不同，_update方式要修改的数据要包裹在 doc 键下
- _update方式不会重复更新，数据已存在不会更新，版本号不会改变，另两种方式会重复更新（覆盖原来数据），版本号会改变
- 这几种方式在更新时都可以增加属性，PUT请求带id更新和POST请求带id更新，会直接覆盖原来的数据，不会在原来的属性里面新增属性

**删除文档&索引**

**删除文档**

接口：`DELETE http://192.168.163.131:9200/customer/external/1`

**删除索引**

接口：`DELETE http://192.168.163.131:9200/customer`

**_bulk-批量操作数据**

语法格式：

```json
{action:{metadata}}\n   // 例如index保存记录，update更新
{request body  }\n

{action:{metadata}}\n
{request body  }\n
```

1. **指定索引和类型的批量操作**

接口：`POST /customer/external/_bulk`

参数：

```json
{"index":{"_id":"1"}}
{"name":"John Doe"}
{"index":{"_id":"2"}}
{"name":"John Doe"}
```

2. **对所有索引执行批量操作**

接口：`POST /_bulk`

参数：

```
{"delete":{"_index":"website","_type":"blog","_id":"123"}}
{"create":{"_index":"website","_type":"blog","_id":"123"}}
{"title":"my first blog post"}
{"index":{"_index":"website","_type":"blog"}}
{"title":"my second blog post"}
{"update":{"_index":"website","_type":"blog","_id":"123"}}
{"doc":{"title":"my updated blog post"}}
```

- 这里的批量操作，当发生某一条执行发生失败时，其他的数据仍然能够接着执行，也就是说彼此之间是独立的。

- bulk api以此按顺序执行所有的action（动作）。如果一个单个的动作因任何原因失败，它将继续处理它后面剩余的动作。

- 当bulk api返回时，它将提供每个动作的状态（与发送的顺序相同），所以您可以检查是否一个指定的动作是否失败了。



#### Elasticsearch-检索进阶

导入样本测试数据；百度官网自行查找

语雀：https://www.yuque.com/docs/share/467684e1-83bc-475d-b063-9224483e738a?#（密码：mxta）

```json
POST bank/account/_bulk
测试数据
```

##### 检索示例介绍

**请求接口**

```json
GET /bank/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "account_number": "asc"
    }
  ]
}
# query 查询条件
# sort 排序条件

GET bank/_search?q=*&sort=account_number:asc
# q=* 查询所有
# sort=account_number:asc 按照account_number进行升序排列
```

ES支持两种基本方式检索:

- 通过REST request uri 发送搜索参数 （uri +检索参数）；
- 通过REST request body 来发送它们（uri+请求体）；

##### Query DSL

###### 基本语法格式

```json
GET bank/_search
{
  "query": {
    "match_all": {}
  },
  "from": 0,
  "size": 5,
  "sort": [
    {
      "account_number": {
        "order": "desc"
      },
      "balance": {
        "order": "asc"
      }
    }
  ],
  "_source": ["balance","firstname"]
}
# match_all 查询类型【代表查询所有的所有】，es中可以在query中组合非常多的查询类型完成复杂查询；
# from+size 限定，完成分页功能；从第几条数据开始，每页有多少数据
# sort 排序，多字段排序，会在前序字段相等时后续字段内部排序，否则以前序为准；
# _source 指定返回结果中包含的字段名
```

###### match-匹配查询

```json
GET bank/_search
{
  "query": {
    "match": {
      "account_number": 20
    }
  }
}
# 查找匹配 account_number 为 20 的数据 非文本推荐使用 term

GET bank/_search
{
  "query": {
    "match": {
      "address": "mill lane"
    }
  }
}
# 查找匹配 address 包含 mill 或 lane 的数据

GET bank/_search
{
  "query": {
    "match": {
      "address.keyword": "288 Mill Street"
    }
  }
}
# 查找 address 为 288 Mill Street 的数据。
# 这里的查找是精确查找，只有完全匹配时才会查找出存在的记录，
# 如果想模糊查询应该使用match_phrase 短语匹配
```

###### match_phrase-短语匹配

将需要匹配的值当成一整个单词（不分词）进行检索

```json
GET bank/_search
{
  "query": {
    "match_phrase": {
      "address": "mill lane"
    }
  }
}
# 这里会检索 address 匹配包含短语 mill lane 的数据
```

###### multi_math-多字段匹配

```json
GET bank/_search
{
  "query": {
    "multi_match": {
      "query": "mill",
      "fields": [
        "city",
        "address"
      ]
    }
  }
}
# 检索 city 或 address 匹配包含 mill 的数据，会对查询条件分词
```

###### bool-复合查询

复合语句可以合并，任何其他查询语句，包括符合语句。这也就意味着，复合语句之间

可以互相嵌套，可以表达非常复杂的逻辑。

- must：必须达到must所列举的所有条件
- must_not，必须不匹配must_not所列举的所有条件。
- should，应该满足should所列举的条件。

```json
GET bank/_search
{
  "query": {
    "bool": {
      "must": [
        {"match": {
          "gender": "M"
        }},{"match": {
          "address": "mill"
        }}
      ],
      "must_not": [
        {"match": {
          "age": "28"
        }}
      ],
      "should": [
        {"match": {
          "lastname": "Hines"
        }}
      ]
    }
  }
}
# 查询 gender 为 M 且 address 包含 mill 的数据
```

###### filter-结果过滤

并不是所有的查询都需要产生分数，特别是哪些仅用于filtering过滤的文档。为了不计算分数，elasticsearch会自动检查场景并且优化查询的执行。

filter 对结果进行过滤，且不计算相关性得分。

```json
GET bank/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "address": "mill"
          }
        }
      ],
      "filter": {
        "range": {
          "balance": {
            "gte": "10000",
            "lte": "20000"
          }
        }
      }
    }
  }
}
# 这里先是查询所有匹配 address 包含 mill 的文档，
# 然后再根据 10000<=balance<=20000 进行过滤查询结果
```

###### term-精确检索

Elasticsearch 官方对于这种非文本字段，使用 term来精确检索是一个推荐的选择。

```json
GET bank/_search
{
  "query": {
    "term": {
      "age": "28"
    }
  }
}
# 查找 age 为 28 的数据
```

###### Aggregation-执行聚合

https://www.elastic.co/guide/en/elasticsearch/reference/7.11/search-aggregations.html

**聚合语法**

```json
GET /my-index-000001/_search
{
  "aggs":{
    "aggs_name":{ # 这次聚合的名字，方便展示在结果集中
        "AGG_TYPE":{ # 聚合的类型(avg,term,terms)
        } 
     }
  }
}
```

**示例1-搜索address中包含mill的所有人的年龄分布以及平均年龄**

```json
GET bank/_search
{
  "query": {
    "match": {
      "address": "Mill"
    }
  },
  "aggs": {
    "ageAgg": {
      "terms": {
        "field": "age",
        "size": 10
      }
    },
    "ageAvg": {
      "avg": {
        "field": "age"
      }
    },
    "balanceAvg": {
      "avg": {
        "field": "balance"
      }
    }
  },
  "size": 0
}
# "ageAgg": {             --- 聚合名为 ageAgg
#   "terms": {            --- 聚合类型为 term
#     "field": "age",     --- 聚合字段为 age
#     "size": 10          --- 取聚合后前十个数据
#   }
# },
# ------------------------
# "ageAvg": {             --- 聚合名为 ageAvg
#   "avg": {              --- 聚合类型为 avg 求平均值
#     "field": "age"      --- 聚合字段为 age
#   }
# },
# ------------------------
# "balanceAvg": {         --- 聚合名为 balanceAvg
#   "avg": {              --- 聚合类型为 avg 求平均值
#     "field": "balance"  --- 聚合字段为 balance
#   }
# }
# ------------------------
# "size": 0               --- 不显示命中结果，只看聚合信息
```

**示例2-按照年龄聚合，并且求这些年龄段的这些人的平均薪资**

```json
GET bank/_search
{
  "query": {
    "match_all": {}
  },
  "aggs": {
    "ageAgg": {
      "terms": {
        "field": "age",
        "size": 100
      },
      "aggs": {
        "ageAvg": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  },
  "size": 0
}
```

**示例3-查出所有年龄分布，并且这些年龄段中M的平均薪资和F的平均薪资以及这个年龄段的总体平均薪资**

```json
GET bank/_search
{
  "query": {
    "match_all": {}
  },
  "aggs": {
    "ageAgg": {
      "terms": {
        "field": "age",
        "size": 100
      },
      "aggs": {
        "genderAgg": {
          "terms": {
            "field": "gender.keyword"
          },
          "aggs": {
            "balanceAvg": {
              "avg": {
                "field": "balance"
              }
            }
          }
        },
        "ageBalanceAvg": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  },
  "size": 0
}
# "field": "gender.keyword" gender是txt没法聚合 必须加.keyword精确替代
```

###### Mapping-映射

https://www.elastic.co/guide/en/elasticsearch/reference/7.11/mapping.html

**创建索引映射**

创建索引并指定属性的映射规则（相当于新建表并指定字段和字段类型）

```json
PUT /my_index
{
  "mappings": {
    "properties": {
      "age": {
        "type": "integer"
      },
      "email": {
        "type": "keyword"
      },
      "name": {
        "type": "text"
      }
    }
  }
}
```

**给已有映射增加字段**

```json
PUT /my_index/_mapping
{
  "properties": {
    "employee-id": {
      "type": "keyword",
      "index": false
    }
  }
}

# 这里的 "index": false，表明新增的字段不能被检索。默认是true
```

**查看映射**

```json
GET /my_index/_mapping
# 查看某一个字段的映射
GET /my_index/_mapping/field/employee-id
```

**更新映射**

> 对于已经存在的字段映射，我们不能更新。更新必须创建新的索引，进行数据迁移。

**数据迁移**

> 无type数据迁移

```json
POST reindex [固定写法]
{
  "source":{
      "index":"twitter"
   },
  "dest":{
      "index":"new_twitters"
   }
}
```

> 有type数据迁移

```json
POST reindex [固定写法]
{
  "source":{
      "index":"twitter",
      "twitter":"twitter"
   },
  "dest":{
      "index":"new_twitters"
   }
}
```

**数据迁移实例**

对于我们的测试数据,是包含 type 的索引 bank。

现在我们创建新的索引 newbank 并修改一些字段的类型来演示当需要更新映射时的数据迁移操作。

1. 查看索引 bank 当前字段映射类型

```json
GET /bank/_mapping
```

2. 创建新索引 newbank 并修改字段类型

```json
PUT /newbank
{
  "mappings": {
    "properties": {
      "account_number": {
        "type": "long"
      },
      "address": {
        "type": "text"
      },
      "age": {
        "type": "integer"
      },
      "balance": {
        "type": "long"
      },
      "city": {
        "type": "keyword"
      },
      "email": {
        "type": "keyword"
      },
      "employer": {
        "type": "keyword"
      },
      "firstname": {
        "type": "text"
      },
      "gender": {
        "type": "keyword"
      },
      "lastname": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      },
      "state": {
        "type": "keyword"
      }
    }
  }
}
```



3. 数据迁移

```json
POST _reindex
{
  "source": {
    "index": "bank",
    "type": "account"
  },
  "dest": {
    "index": "newbank"
  }
}
```

4. 查看迁移后的数据

```json
GET /newbank/_search
```

#### Elasticsearch-分词

https://www.elastic.co/guide/en/elasticsearch/reference/7.x/analysis.html

```json
POST _analyze
{
  "analyzer": "standard",
  "text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone."
}
```

默认的分词器一般都是针对于英文，对于中文我们需要安装额外的分词器来进行分词。

##### 安装IK分词器

事前准备：

- IK 分词器属于 Elasticsearch 的插件，所以 IK 分词器的安装目录是 Elasticsearch 的 plugins 目录，在我们使用Docker启动 Elasticsearch 时，已经将该目录挂载到主机的 `/mydata/elasticsearch/plugins` 目录。
- IK 分词器的版本需要跟 Elasticsearch 的版本对应，当前选择的版本为 `7.4.2`，下载地址为：[Github Release](https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v7.4.2) 或访问：[镜像地址](https://hub.fastgit.org/medcl/elasticsearch-analysis-ik/releases/tag/v7.4.2)

```bash
# 进入挂载的插件目录 /mydata/elasticsearch/plugins
cd /mydata/elasticsearch/plugins

# 安装 wget 下载工具
 yum install -y wget

# 下载对应版本的 IK 分词器（这里是7.4.2）
wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.4.2/elasticsearch-analysis-ik-7.4.2.zip

# 解压到 plugins 目录下的 ik 目录
unzip elasticsearch-analysis-ik-7.4.2.zip -d ik

# 修改文件夹访问权限
chmod -R 777 ik/

# 进入 es 容器内部
docker exec -it elasticsearch /bin/bash
# 进入 es bin 目录
cd /usr/share/elasticsearch/bin

# 执行查看命令  显示 ik
elasticsearch-plugin list

# 退出容器
exit

# 重启 Elasticsearch
docker restart elasticsearch
```

**测试 ik 分词器**

```json
GET my_index/_analyze
{
   "analyzer": "ik_max_word", 
   "text":"蔡徐坤"
}
```

这里对于默认词库中没有的词，不会有词语的组合，所以我们可以通过配置自定义词库或远程词库来实现对词库的扩展。

##### 自定义扩展分词库

nginx 中自定义分词文件

echo "蔡徐坤" > /mydata/nginx/html/fenci.txt

如果想要增加新的词语，只需要在该文件追加新的行并保存新的词语即可。

打开并编辑 ik 插件配置文件
vim /mydata/elasticsearch/plugins/ik/config/IKAnalyzer.cfg.xml

修改远程扩展字典

重启 elasticsearch 容器

#### Elasticsearch-项目整合