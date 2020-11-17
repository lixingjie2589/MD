# Redis     基于Linux

## Redis入门

### 概述

> Redis是什么？

Redis（Remote Dictionary Server )，即远程字典服务，是一个开源的使用ANSI [C语言](https://baike.baidu.com/item/C语言)编写、支持网络、可基于内存亦可持久化的日志型、Key-Value[数据库](https://baike.baidu.com/item/数据库/103728)，并提供多种语言的API。

> Redis能干嘛？

1. 数据存储、持久化（rdb、aof）
2. 效率高，可用于高数缓存
3. 发布订阅系统
4. 地图信息分析
5. 计时器、计数器（浏览量）
6. ……

> Redis特性

1. 多样化数据类型
2. 持久化
3. 集群
4. 事务
5. ……

> 学习要用到东西

1. 官网：https://redis.io/
2. 中文网：http://www.redis.cn/

### Linux安装Reddis

1、下载安装包；

2、解压Redis安装包； tar -zxvf 文件名  ；一般在/opt目录下

3、 进入解压后的文件，可以看到redis.conf配置文件

4、安装基本的环境

```shell
yum install gcc-c++
gcc -v  #查看版本
```

在解压后的目录下执行

```shell
make 
make install  # 安装

#安装6.0以上版本需要升级gcc到5.3及以上,如下：升级到gcc 9.3：
yum -y install centos-release-scl
yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
scl enable devtoolset-9 bash
#需要注意的是scl命令启用只是临时的，退出shell或重启就会恢复原系统gcc版本。
#如果要长期使用gcc 9.3的话：
echo "source /opt/rh/devtoolset-9/enable" >>/etc/profile
#这样退出shell重新打开就是新版的gcc了
```

5、默认安装路径`/usr/local/bin`;

6、把redis的配置文件复制出来，以后修改这个配置文件，搞坏了可以找以前的配置文件

7、redis默认不是后台运行，可以在配置文件中配置

```shell
daemonize no  # 改为yes就表示后台启动
```

8、测试redis服务

```shell
[root@localhost bin]# redis-server sconfig/redis.conf  # 在Redis安装所在目录运行，sconfig/redis.conf通过指定的配置文件启动
[root@localhost bin]# redis-cli -p 6379  #使用Redis客户端链接
127.0.0.1:6379> ping  #是否启动成功
PONG
127.0.0.1:6379> set name sanil
OK
127.0.0.1:6379> get name
"sanil"
```

9、查看Redis的进程是否开启

```shell
ps -ef|grep redis
```

10、关闭Redis服务

```shell
127.0.0.1:6379> shutdown
not connected> 
```

### 测试性能

`redis-benchmark`是官方自带压力测试工具

![菜鸟赞助](.\image\RedisImage\20201109.png)

自测：

```shell
redis-benchmark -c 100 -n 100000 # 效果图自行脑补
```

### 基础知识

redis默认有16个数据库，默认使用的是第0个；

```bash
127.0.0.1:6379> select 3  #切换数据库
OK
127.0.0.1:6379[3]> dbsize  #查看数据库大小
(integer) 0
127.0.0.1:6379> keys *  #查看所有的key
1) "name"
127.0.0.1:6379> flushdb  # 清空当前数据库
OK
127.0.0.1:6379[1]> flushall  # 清空全部数据库
OK
```

> Redis是单线程的！

Redis是基于内存操作，CPU不是Redis性能瓶颈，Redis的瓶颈是受机器内存和网络带宽影响；

**Redis是单线程还这么快？**

1、高性能的服务器不一定是多线程的；

2、多线程不一定比单线程效率高；

……

## 五大数据类型

### Redis-Key

```bash
127.0.0.1:6379> exists name  # 判断当前key是否存在
(integer) 1
127.0.0.1:6379> move name 1  # 移除当前key
(integer) 1
127.0.0.1:6379> expire name 5  #设置key的过期时间
(integer) 1
127.0.0.1:6379> ttl name  # 查看当前key的剩余时间
(integer) 2
127.0.0.1:6379> ttl name # -2过期
(integer) -2
127.0.0.1:6379> type name  # 查看当前key类型
string
```



### String(字符串)

```bash
127.0.0.1:6379> APPEND k1 hello  #往某个key追加value，key不存在就等于set
(integer) 7
127.0.0.1:6379> STRLEN k1  # 获取某个key的value长度
(integer) 7
=======================================================
# i++
127.0.0.1:6379> set count 0  # 设置初始值
OK
127.0.0.1:6379> get count
"0"
127.0.0.1:6379> incr count  # 自增1
(integer) 1
127.0.0.1:6379> incr count
(integer) 2
127.0.0.1:6379> get count
"2"
127.0.0.1:6379> decr count  # 自减1
(integer) 1
127.0.0.1:6379> decr count
(integer) 0
127.0.0.1:6379> get count
"0"
127.0.0.1:6379> incrby count 10  # 自定增量
(integer) 10
127.0.0.1:6379> incrby count 10
(integer) 20
127.0.0.1:6379> decrby count 5  # 自定减量
(integer) 15
127.0.0.1:6379> decrby count 5
(integer) 10
=======================================================
# 字符串范围
127.0.0.1:6379> GETRANGE k1 0 3  # 截取字符串
"snai"
127.0.0.1:6379> GETRANGE k1 0 -1  # 获取全部字符串
"snail,ll"

# 替换
127.0.0.1:6379> SETRANGE k1 1 xx  # 替换指定位置开始的字符串
(integer) 8
127.0.0.1:6379> get k1
"sxxil,ll"
=======================================================
# setex(set with expire)  设置过期时间
# setnx(set if not exist) 不存在在设置 (在分布式锁中常常使用)
127.0.0.1:6379> setex k1 10 hhh  # 设置k1的值为hhh，10秒后过期
OK
127.0.0.1:6379> setnx k1 ggg  # 如果k1不存在则创建k1
(integer) 1
127.0.0.1:6379> setnx k1 hhh  # 如果k1存在则创建失败
(integer) 0
=====================================================
# mset
# mget
127.0.0.1:6379> mset k1 hhh k2 ggg  # 同时设置多个值
OK
127.0.0.1:6379> mget k1 k2  # 同时获取多个值
1) "hhh"
2) "ggg"
127.0.0.1:6379> msetnx k1 fff k2 ddd  # 要么一起成功，要么一起失败
(integer) 0

# 对象
set user:1{name:zhangsan,age:3} # 设置一个user:1对象 值为json字符来保存一个对象

# 很好的key设计： user:{id}:{filed}
127.0.0.1:6379> mset user:1:name zs user:1:age 3
OK
127.0.0.1:6379> mget user:1:name user:1:age
1) "zs"
2) "3"
=============================
getset  #先get在set
127.0.0.1:6379> getset jj kk
(nil)
127.0.0.1:6379> getset jj ll
"kk"
```



### List(列表)

在redis中，list可以是栈、队列、阻塞队列！

所有的list命令都是用L开头的

```bash
127.0.0.1:6379> LPUSH list one  #将一个值插入列表头部 左
(integer) 1
127.0.0.1:6379> RPUSH list rigth  # 将一个值插入列表尾部 右
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1  # 获取list中的值
1) "three"
2) "two"
3) "one"
4) "rigth"
============================
127.0.0.1:6379> LPOP list #移除list的第一个值
127.0.0.1:6379> RPOP list #移除list的最后一个值
127.0.0.1:6379> LREM list 1 one  # 移除指定个数的value
(integer) 1
==============================
127.0.0.1:6379> LINDEX list 0  # 通过下标获得list中的某一个值
"three"
127.0.0.1:6379> LLEN list  # 返回列表长度
(integer) 3
======================
127.0.0.1:6379> lrange list 0 -1
1) "one"
2) "three"
3) "two"
127.0.0.1:6379> LTRIM list 0 1  # 通过下标截取指定的长度，这个list已经被改变，只剩下截断的元素
OK
127.0.0.1:6379> lrange list 0 -1
1) "one"
2) "three"
========================
127.0.0.1:6379> lrange list 0 -1
1) "one"
2) "three"
127.0.0.1:6379> RPOPLPUSH list mylist  # 移除列表的最后一个元素，并移动到新的列表中
"three"
127.0.0.1:6379> lrange mylist 0 -1
1) "three"
===================
127.0.0.1:6379> LSET list 0 hhh  # 将列表中指定下标的值替换为另一个值，列表不存在报错，下标不存在报错
OK
=====================
LINSERT # 将value插入到列表中某个元素的前面或者后面
127.0.0.1:6379> LINSERT list before word other
(integer) 3
127.0.0.1:6379> LINSERT list after word other
(integer) 4
```

> 小结

- 它是实际上是一个链表，before Node after，left ，rigth都可以插入值
- 如果key不存在则创建新的链表；存在就新增内容
- 移除链表的所有值，空链表也代表不存在
- 在两边插入或者改动值，效率最高；中间元素相对效率会低一些

### Set(集合)

```bash
127.0.0.1:6379> SADD set hello  # set集合中添加元素
(integer) 1
127.0.0.1:6379> SADD set word
(integer) 1
127.0.0.1:6379> SMEMBERS set  # 查看指定的set的所有值
1) "word"
2) "hello"
127.0.0.1:6379> SISMEMBER set hello  # 判断某一个值是不是在set集合中
(integer) 1
127.0.0.1:6379> SISMEMBER set hellol
(integer) 0
127.0.0.1:6379> SCARD set  # 获取set集合中元素个数
(integer) 2
127.0.0.1:6379> SREM set hello  # 移除set集合中的指定元素
(integer) 1
127.0.0.1:6379> SRANDMEMBER set  # 随机抽取出一个元素
"hello"
127.0.0.1:6379> SRANDMEMBER set 2  # 随机抽取出指定个数的元素
1) "word"
2) "txt"
127.0.0.1:6379> SPOP set  # 随机删除一些set集合中的元素，后面可以指定个数
"word"
127.0.0.1:6379> SMOVE set set2 txt  # 将一个指定的值移动到另一个set集合
(integer) 1

127.0.0.1:6379> sdiff k1 k2  # 差集
1) "a"
2) "b"
127.0.0.1:6379> SINTER k1 k2  # 交集
1) "c"
127.0.0.1:6379> SUNION k1 k2  # 并集
1) "a"
2) "b"
3) "c"
4) "d"
5) "e"

```



### Hash(哈希)

```bash
127.0.0.1:6379> hset hash k1 snail  # set一个k-v
(integer) 1
127.0.0.1:6379> hget hash k1  # 获取一个字段值
"snail"
127.0.0.1:6379> hmset hash k1 ss k2 hh  # set多个k-v
OK
127.0.0.1:6379> hmget hash k1 k2 # get多个字段值
1) "ss"
2) "hh"
127.0.0.1:6379> HGETALL hash  # 获取全部的数据
1) "k1"
2) "ss"
3) "k2"
4) "hh"
127.0.0.1:6379> HDEL hash k1  # 删除hash指定key字段！对应的value也删除
(integer) 1
127.0.0.1:6379> HLEN hash  # 获取hash表的字段数量
(integer) 1
 
127.0.0.1:6379> HEXISTS hash k2  # 判断hash中指定字段是否存在
(integer) 1
127.0.0.1:6379> HEXISTS hash k1
(integer) 0
127.0.0.1:6379> 

127.0.0.1:6379> hkeys hash  # 获取所有的field
1) "k2"
127.0.0.1:6379> HVALS hash  # 获取所有的value
1) "hh"

127.0.0.1:6379> HINCRBY hash k3 1  # 指定增量
(integer) 6
127.0.0.1:6379> HINCRBY hash k3 -1
(integer) 6

127.0.0.1:6379> hsetnx hash k3 hello  # 不存在可以设置，存在就不能设置
(integer) 0

```



### Zset(有序集合)

```bash
127.0.0.1:6379> ZADD zset 1 one  # 添加一个值
(integer) 1
127.0.0.1:6379> ZADD zset 2 two 3 three  # 添加多个值
(integer) 2
127.0.0.1:6379> zrange zset 0 -1  # 查看
1) "one"
2) "two"
3) "three"

127.0.0.1:6379> ZRANGEBYSCORE zset -inf +inf  # 显示全部用户，从小到大
1) "one"
2) "two"
3) "three"
127.0.0.1:6379> ZREVRANGE zset 0 -1   # 显示全部用户，从大到小
1) "two"
2) "one"
127.0.0.1:6379> ZRANGEBYSCORE zset -inf +inf withscores  #显示全部用户和成绩
1) "one"
2) "1"
3) "two"
4) "2"
5) "three"
6) "3"
127.0.0.1:6379> ZRANGEBYSCORE zset -inf 2 withscores # 显示小于等于2的值
1) "one"
2) "1"
3) "two"
4) "2"

127.0.0.1:6379> ZREM zset three  # 移除指定元素
(integer) 1
127.0.0.1:6379> ZCARD zset  # 获取集合中的个数
(integer) 2
127.0.0.1:6379> ZCOUNT zset 1 3  # 获取指定区间的成员个数
(integer) 3

```



## 三种特殊数据类型

### Geospatial(地理位置)

朋友定位，附近的人，打车距离计算？

测试数据：http://www.jsons.cn/lngcode/

> [GEOADD](http://www.redis.cn/commands/geoadd.html)

```bash
# 将指定的地理空间位置（纬度、经度、名称）添加到指定的`key`中
# 两极无法直接添加，可以通过java程序一次性导入
127.0.0.1:6379> GEOADD city 121.47 31.23 shanghai
(integer) 1
```

>[GEOPOS](http://www.redis.cn/commands/geopos.html)

```bash
# 从`key`里返回所有给定位置元素的位置（经度和纬度）
127.0.0.1:6379> geopos city shanghai
1) 1) "121.47000163793563843"
   2) "31.22999903975783553"
```

>  [GEODIST](http://www.redis.cn/commands/geodist.html)

```bash
# 返回两个给定位置之间的直线距离
# 指定单位的参数 unit 必须是以下单位的其中一个，默认是米：
# m 表示单位为米。
# km 表示单位为千米。
# mi 表示单位为英里。
# ft 表示单位为英尺。
127.0.0.1:6379> GEODIST city shanghai beijing km
"1067.3788"

```

> [GEORADIUS](http://www.redis.cn/commands/georadius.html)

以给定的经纬度为中心， 返回键包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素。

范围可以使用以下其中一个单位：

- **m** 表示单位为米。
- **km** 表示单位为千米。
- **mi** 表示单位为英里。
- **ft** 表示单位为英尺。

在给定以下可选项时， 命令会返回额外的信息：

- `WITHDIST`: 在返回位置元素的同时， 将位置元素与中心之间的距离也一并返回。 距离的单位和用户给定的范围单位保持一致。

- `WITHCOORD`: 将位置元素的经度和维度也一并返回。

- `WITHHASH`: 以 52 位有符号整数的形式， 返回位置元素经过原始 geohash 编码的有序集合分值。 这个选项主要用于底层应用或者调试， 实际中的作用并不大。

命令默认返回未排序的位置元素。 通过以下两个参数， 用户可以指定被返回位置元素的排序方式：

  - `ASC`: 根据中心的位置， 按照从近到远的方式返回位置元素。
  - `DESC`: 根据中心的位置， 按照从远到近的方式返回位置元素。
  - `COUNT`: 返回指定个数

```bash
127.0.0.1:6379> GEORADIUS city 110 30 500 km withdist withcoord count 2 asc 
1) 1) "chongqin"
   2) "341.9374"
   3) 1) "106.49999767541885376"
      2) "29.52999957900659211"
2) 1) "xian"
   2) "483.8340"
   3) 1) "108.96000176668167114"
      2) "34.25999964418929977"
```

> [GEORADIUSBYMEMBER](http://www.redis.cn/commands/georadiusbymember.html)

这个命令和 GEORADIUS 命令一样,但是 `GEORADIUSBYMEMBER` 的中心点是由给定的位置元素决定的

```bash
127.0.0.1:6379> GEORADIUSBYMEMBER city beijing 100 km
1) "beijing"
```

> [GEOHASH](http://www.redis.cn/commands/geohash.html)

该命令将返回11个字符的Geohash字符串

```bash
127.0.0.1:6379> GEOHASH city beijing
1) "wx4fbxxfke0"
```

> GEO底层的实现原理其实是Zset；我们可以使用Zset命令来操作GEO

```bash
127.0.0.1:6379> ZRANGE city 0 -1
1) "chongqin"
2) "xian"
3) "hangzhou"
4) "shanghai"
5) "beijing"
127.0.0.1:6379> ZREM city xian
(integer) 1
```

### Hyperloglog

HyperLoglog是一种用于计数唯一事物的概率数据结构(从技术上讲，这是指估计集合的基数)。

可以用于网页的UV

有一定误差，如果不允许出现错误可以使用set或自己的数据类型

```bash
127.0.0.1:6379> PFADD k1 a b c d e f f  # 创建第一组元素
(integer) 1
127.0.0.1:6379> PFCOUNT k1  # 统计元素的基数个数
(integer) 6
127.0.0.1:6379> PFADD k2 e f h i j k 
(integer) 1
127.0.0.1:6379> PFCOUNT k2
(integer) 6
127.0.0.1:6379> PFMERGE k3 k1 k2  # 合并 并集
OK
127.0.0.1:6379> PFCOUNT k3  # 看并集数量
(integer) 10

```

### Bitmap

Bit 操作分为两组：固定时间的单Bit 操作，如将位设置为1或0，或获取其值，以及对Bit 组的操作，例如，计算给定Bit 范围内的集合位数(例如，人口计数)；两种状态的都可以使用Bitmap；

```bash
127.0.0.1:6379> SETBIT si 0 1  #新增
(integer) 0
127.0.0.1:6379> SETBIT si 1 0
(integer) 0
127.0.0.1:6379> GETBIT si 0  # 获取
(integer) 1
127.0.0.1:6379> GETBIT si 1
(integer) 0
127.0.0.1:6379> BITCOUNT si  #统计
(integer) 1
```

## 事务

**Redis事务本质：一组命令的集合！一个事务中的所有命令都会被序列化，在事务执行过程中，就会按照顺序执行！一次性、顺序性、排他性**

**Redis事务没有隔离级别的概念**

**Redis单挑命令是保存原子性的，但是事务不保证原子性；**

redis的事务：

- 开启事务（multi）
- 命令入队（……）
- 执行事务（exec）

>正常执行事务

```bash
127.0.0.1:6379> multi  # 开启事务
OK
127.0.0.1:6379> set k1 v1  # 命令入队
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k1
QUEUED
127.0.0.1:6379> exec  # 执行事务
1) OK
2) OK
3) "v1"


127.0.0.1:6379> DISCARD  # 取消事务，队列中的命令都不会执行
OK
```

编译型异常（代码有问题！命令有错！），事务中的所有命令都不会执行！

运行时异常，事务执行时错误的命令不影响其他命令的运行

> **监控**  watch

**悲观锁**

- 很悲观，认为什么时候都会出问题，无论做什么都会加锁!

**乐观锁**

- 很乐观，认为什么时候都不会出问题，所以不会上锁！更新的时候去判断一下在此期间是否有人修改过这个数据
- 获取version
- 更新时比较version

```bash
127.0.0.1:6379> watch k1  #加锁
OK
127.0.0.1:6379> UNWATCH  #解锁
OK
```

## Jedis

**Jedis是使用Java操作Redis的中间件**

1、导入对应的依赖

```xml
		<dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.3.0</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.62</version>
        </dependency>
```

2、编码测试

…………详见代码

## SpringBoot整合

在SpringBoot2.0之后，原来使用的jedis被替换为lettuce

jedis：采用直连，多个线程操作的话是不安全的，如果要避免不安全，可以使用jedis pool连接池；更像BIO模式；

lettuce：采用netty，实列可以在多个线程中进行共享，不存在线程不安全的情况；可以减少线程数据，更像NIO模式；

> 整合测试

1、导入依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

2、配置连接

```properties
#配置redis
spring.redis.host=192.168.35.128
spring.redis.port=6379
```

3、测试代码见项目……

## Redis.conf详解

启动的时候通过配置文件启动！

> 单位

```bash
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes
#
# units are case insensitive so 1GB 1Gb 1gB are all the same.
```

配置文件unit单位对大小写不敏感！

> 包含  可以与其他配置文件一起

```bash
# include /path/to/local.conf
# include /path/to/other.conf
```

> 网络

```bash
bind 127.0.0.1  # 绑定的ip
protected-mode yes  # 保护模式
port 6379  # 端口设置
```

> 通用GENERAL

```bash
daemonize no  # 以守护进程的方式运行，默认是no，需手动改为yes
pidfile /var/run/redis_6379.pid  # 如果以后台的方式运行，我们就需要指定一个pid文件

# 日志
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably) 生产环境
# warning (only very important / critical messages are logged)
loglevel notice
logfile ""  # 日志的文件位置
databases 16  #默认数据库的数量，16个
always-show-logo yes  # 是否总是显示LOGO
```

> 快照

持久化，在规定内，执行了多少次操作，则会持久化到文件 .rdb .aof；

redis是内存数据库，如果没有持久化，那么断电即失；

```bash
save 900 1  # 如果900秒内至少有 1个key进行了修改，就进行持久化
save 300 10  # 如果300秒内至少有 10个key进行了修改，就进行持久化
save 60 10000  # 如果60秒内至少有 10000个key进行了修改，就进行持久化

stop-writes-on-bgsave-error yes # 持久化出错，是否继续工作

rdbcompression yes  # 是否压缩rdb文件，需要消耗一些cpu资源

rdbchecksum yes  # 保存rdb文件的时候，进行错误的检验

dir ./  # rdb文件保存的目录
```

> REPLICATION 复制

……

> SECURITY 安全

可以设置redis的密码，默认是没有密码；

```bash
127.0.0.1:6379> config get requirepass # 获取redis密码
1) "requirepass"
2) ""
127.0.0.1:6379> config set requirepass "123456" # 设置redis密码
OK
127.0.0.1:6379> auth 123456 # 使用密码进行登录
OK
```

> CLIENTS 限制

```bash
maxclients 10000  # 设置能链接上redis的最大客户端的数量
maxmemory <bytes> # redis配置最大的内存容量
maxmemory-policy noeviction # 内存到达上限之后的处理策略
1、默认为 noeviction   ：这个策略是说如果redis数据库达到最大内存时会不进行置换key，但是会返回给客户端一个错误信息

2、volatile-lru:对生存周期内很少有使用key进行置换

3、volatile-random：对生存周期中的key进行随机置换

4、volatile-ttl：对生存周期内的key随机进行抽取，在这个抽取中取出生存周期最不常用的key进行置换

5、allkeys-random：对整个数据库的key进行随机置换

6、allkeys-lru：置换整个数据库中最少使用的key
```

> APPEND ONLY MODE 模式aof

```bash
appendonly no  # 默认不开启
appendfilename "appendonly.aof"  # 持久化文件名

# appendfsync always  每次修改都会sync，消耗性能
appendfsync everysec  # 每秒执行一次sync，可能会丢失这一秒的数据
# appendfsync no  不执行sync，这个时候操作系统自己同步数据，速度最快

```

## Redis持久化

Redis的数据都存放在内存中，如果没有配置持久化，redis重启后数据就全丢失了，于是需要开启redis的持久化功能，将数据保存到磁盘上，当redis重启后，可以从磁盘中恢复数据。

redis提供两种方式进行持久化：

RDB持久化是指在指定的时间间隔内将内存中的数据集快照写入磁盘，实际操作过程是fork一个子进程，先将数据集写入临时文件，写入成功后，再替换之前的文件，用二进制压缩存储。

**优点：**

1、适合大规模的数据恢复；

2、对数据的完整性不高；

**缺点：**

1、需要一定的时间间隔进行操作，如果redis意外宕机了，最后一次修改的数据就没有了；

2、fork进程的时候会占用一定的内存空间；



AOF持久化以日志的形式记录服务器所处理的每一个写、删除操作，查询操作不会记录，以文本的方式记录，可以打开文件看到详细的操作记录。

**优点：**

1、每一次修改都同步，文件的完整性更好；

2、每秒同步一次，可能会丢失一秒的数据；

**缺点：**

1、现对于数据文件来说，aof远远大于rdb，修复速度也比rdb慢；

2、aof运行效率也要比rdb慢；

## Redis发布订阅

Redis 发布订阅 (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息。

Redis 客户端可以订阅任意数量的频道。

Redis 发布订阅命令：

| 1    | [ PSUBSCRIBE pattern [pattern ...\]](https://www.runoob.com/redis/pub-sub-psubscribe.html)  订阅一个或多个符合给定模式的频道。 |
| ---- | ------------------------------------------------------------ |
| 2    | [PUBSUB subcommand [argument [argument ...\]]](https://www.runoob.com/redis/pub-sub-pubsub.html)  查看订阅与发布系统状态。 |
| 3    | [PUBLISH channel message](https://www.runoob.com/redis/pub-sub-publish.html)  将信息发送到指定的频道。 |
| 4    | [PUNSUBSCRIBE [pattern [pattern ...\]]](https://www.runoob.com/redis/pub-sub-punsubscribe.html)  退订所有给定模式的频道。 |
| 5    | [SUBSCRIBE channel [channel ...\]](https://www.runoob.com/redis/pub-sub-subscribe.html)  订阅给定的一个或多个频道的信息。 |
| 6    | [UNSUBSCRIBE [channel [channel ...\]]](https://www.runoob.com/redis/pub-sub-unsubscribe.html)  指退订给定的频道。 |

> 测试

订阅端：

```bash
127.0.0.1:6379> SUBSCRIBE snail  # 订阅一个频道
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "snail"
3) (integer) 1
# 等待读取推送的消息
1) "message" # 消息
2) "snail" # 来源
3) "hello,snail" # 具体内容
```

发送端：

```bash
127.0.0.1:6379> PUBLISH snail "hello,snail" # 发布者发布消息到频道
(integer) 1
```

## Redis 主从复制

### 概念

主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点(master)，后者称为从节点(slave)；数据的复制是单向的，只能由主节点到从节点。

默认情况下，每台Redis服务器都是主节点；且一个主节点可以有多个从节点(或没有从节点)，但一个从节点只能有一个主节点。

### 主从复制的作用

1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务的冗余。
3. 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务（即写Redis数据时应用连接主节点，读Redis数据时应用连接从节点），分担服务器负载；尤其是在写少读多的场景下，通过多个从节点分担读负载，可以大大提高Redis服务器的并发量。
4. 高可用基石：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础，因此说主从复制是Redis高可用的基础。

### 环境配置

只配置从库，不用配置主库；

```bash
127.0.0.1:6379> info replication  # 查看当前库的信息
# Replication
role:master  # 角色
connected_slaves:0  # 从机数
master_replid:1184212a631c5a0711f414b8d9d6db52257483b8
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

复制3个配置文件，然后修改对应信息：

1、端口

2、pid 名字

3、log 文件名

4、dump.rdb 文件名

修改后启动3个redis服务：

```bash
[root@localhost bin]# ps -ef|grep redis
root      12913      1  0 13:54 ?        00:00:00 redis-server *:6379
root      12933      1  0 13:54 ?        00:00:00 redis-server *:6380
root      12943      1  0 13:54 ?        00:00:00 redis-server *:6381
root      12995  12303  0 13:55 pts/3    00:00:00 grep --color=auto redis
```

### 一主二从

认谁做老大！

```bash
127.0.0.1:6380> SLAVEOF 127.0.0.1 6379  # 在6380节点执行slaveof命令，使之变为从节点
OK

127.0.0.1:6380> info replication
# Replication
role:slave  # 变为从机
master_host:127.0.0.1  #可以看到主机配置
master_port:6379
master_link_status:up
master_last_io_seconds_ago:4
master_sync_in_progress:0
slave_repl_offset:14
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:e193e2b8f0019d0e3c609f242a58c77a466169e3
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:14
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:14

# 在主机中查看
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:1 # 可以看到从机的配置
slave0:ip=127.0.0.1,port=6380,state=online,offset=28,lag=1
master_replid:e193e2b8f0019d0e3c609f242a58c77a466169e3
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:28
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:28
```

真实的主从配置应该在配置文件中配置，这样的话就是永久的，上面是通过命令配置，暂时的；

> 提示

主机可以写，从机不能写只能读；主机中的所有信息和数据都会自动被从机保存；

```bash
127.0.0.1:6380> keys *
1) "k1"
127.0.0.1:6380> set k1 v2
(error) READONLY You can't write against a read only replica.
```

测试：主机断开连接，从机依旧连接到主机，但是没有写操作，主机如果又连接上了，从机依旧可以获取到主机写的信息；

使用命令配置的主从，从机重启后会变成主机，需重新配置；

> 复制原理

Redis的主从复制功能除了支持一个Master节点对应多个Slave节点的同时进行复制外，还支持Slave节点向其它多个Slave节点进行复制。这样使得我们能够灵活组织业务缓存数据的传播，例如使用多个Slave作为数据读取服务的同时，专门使用一个Slave节点为流式分析工具服务。Redis的主从复制功能分为两种数据同步模式进行：全量数据同步和增量数据同步。

**全量数据同步：**

先执行一次全同步 — 请求master BgSave出自己的一个RDB Snapshot文件发给slave，slave接收完毕后，清除掉自己的旧数据，然后将RDB载入内存。

**增量数据同步：**
再进行增量同步 — master作为一个普通的client连入slave，将所有写操作转发给slave，没有特殊的同步协议。

**火车模式**

redsi一个接一个，一对多变为一对一；

如果主机挂了，从机无法自动变问主机，需要手动命令：

```bash
127.0.0.1:6380> SLAVEOF no one  #使自己变为主机
OK
```

如果以前的主机修复了，那也需要重新配置；

### 哨兵模式

**主从切换技术的方法是：当主服务器宕机后，需要手动把一台从服务器切换为主服务器，这就需要人工干预，费事费力，还会造成一段时间内服务不可用。**这不是一种推荐的方式，更多时候，我们优先考虑**哨兵模式**。

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，它会独立运行。其原理是**哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例。**

![20201117](.\image\RedisImage\20201117.png)

这里的哨兵有两个作用

- 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。
- 当哨兵监测到master宕机，会自动将slave切换成master，然后通过**发布订阅模式**通知其他的从服务器，修改配置文件，让它们切换主机。

然而一个哨兵进程对Redis服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控，这样就形成了多哨兵模式。

![202011171450](.\image\RedisImage\202011171450.png)

> 测试

1、配置哨兵配置文件 sentinel.conf

```bash
#sentinel monitor 被监控的名 host port 1
sentinel monitor myredis 127.0.0.1 6379 1
# 后面的数字1 代表主机挂了，slave投票看让谁接替成为主机，票数多的为主机
```

以上是简单配置……

2、启动哨兵

```bash
[root@localhost bin]# redis-sentinel sconfig/sentinel.conf
```

优点：高可用，在主节点故障时能实现故障的转移
缺点：
　　1)好像没办法做到水平拓展，如果内容很大的情况下。
　　2)主从服务器的数据要经常进行主从复制，这样造成性能下降。
　　3)当主服务器宕机后，从服务器切换成主服务器的那段时间，服务是不能用的。



## Redis的缓存穿透和雪崩

**初识**

- 缓存穿透：key对应的数据在数据源并不存在，每次针对此key的请求从缓存获取不到，请求都会到数据源，从而可能压垮数据源。比如用一个不存在的用户id获取用户信息，不论缓存还是数据库都没有，若黑客利用此漏洞进行攻击可能压垮数据库。
- 缓存击穿：key对应的数据存在，但在redis中过期，此时若有大量并发请求过来，这些请求发现缓存过期一般都会从后端DB加载数据并回设到缓存，这个时候大并发的请求可能会瞬间把后端DB压垮。
- 缓存雪崩：当缓存服务器重启或者大量缓存集中在某一个时间段失效，这样在失效的时候，也会给后端系统(比如DB)带来很大压力。

### 缓存穿透（查不到）

> 解决方案

**布隆过滤器**

布隆过滤器可以用于检索一个元素是否在一个集合中。

一个一定不存在缓存及查询不到的数据，由于缓存是不命中时被动写的，并且出于容错考虑，如果从存储层查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到存储层去查询，失去了缓存的意义。

使用场景有：黑名单校验

**缓存空对象**

1、空值做了缓存，意味着缓存层中存了更多的键，需要更多内存空间。

比较有效的方法是针对这类数据设置一个较短的过期时间。

2、缓存层和存储层的数据会有一段时间窗口的不一致，可能会对业务有一定影响。

### 缓存击穿（量太多）

> 解决方案

**后台刷新**

后台定义一个job(定时任务)专门主动更新缓存数据.比如,一个缓存中的数据过期时间是30分钟,那么job每隔29分钟定时刷新数据(将从数据库中查到的数据更新到缓存中).

这种方案比较容易理解，但会增加系统复杂度。比较适合那些 key 相对固定,cache 粒度较大的业务，key 比较分散的则不太适合，实现起来也比较复杂。

**检查更新**

将缓存key的过期时间(绝对时间)一起保存到缓存中(可以拼接,可以添加新字段,可以采用单独的key保存..不管用什么方式,只要两者建立好关联关系就行).在每次执行get操作后,都将get出来的缓存过期时间与当前系统时间做一个对比,如果缓存过期时间-当前系统时间<=1分钟(自定义的一个值),则主动更新缓存.这样就能保证缓存中的数据始终是最新的(和方案一一样,让数据不过期.)

这种方案在特殊情况下也会有问题。假设缓存过期时间是12:00，而 11:59
到 12:00这 1 分钟时间里恰好没有 get 请求过来，又恰好请求都在 11:30 分的时
候高并发过来，那就悲剧了。这种情况比较极端，但并不是没有可能。因为“高
并发”也可能是阶段性在某个时间点爆发。

**分级缓存**

采用 L1 (一级缓存)和 L2(二级缓存) 缓存方式，L1 缓存失效时间短，L2 缓存失效时间长。 请求优先从 L1 缓存获取数据，如果 L1缓存未命中则加锁，只有 1 个线程获取到锁,这个线程再从数据库中读取数据并将数据再更新到到 L1 缓存和 L2 缓存中，而其他线程依旧从 L2 缓存获取数据并返回。

这种方式，主要是通过避免缓存同时失效并结合锁机制实现。所以，当数据更
新时，只能淘汰 L1 缓存，不能同时将 L1 和 L2 中的缓存同时淘汰。L2 缓存中
可能会存在脏数据，需要业务能够容忍这种短时间的不一致。而且，这种方案
可能会造成额外的缓存空间浪费。

**互斥锁**

```java
static Lock reenLock = new ReentrantLock();

    public List<String> getData04() throws InterruptedException {
        List<String> result = new ArrayList<String>();
        // 从缓存读取数据
        result = getDataFromCache();
        if (result.isEmpty()) {
            if (reenLock.tryLock()) {
                try {
                    System.out.println("我拿到锁了,从DB获取数据库后写入缓存");
                    // 从数据库查询数据
                    result = getDataFromDB();
                    // 将查询到的数据写入缓存
                    setDataToCache(result);
                } finally {
                    reenLock.unlock();// 释放锁
                }

            } else {
                result = getDataFromCache();// 先查一下缓存
                if (result.isEmpty()) {
                    System.out.println("我没拿到锁,缓存也没数据,先小憩一下");
                    Thread.sleep(100);// 小憩一会儿
                    return getData04();// 重试
                }
            }
        }
        return result;
    }
```

使用互斥锁的方式来实现，可以有效避免前面几种问题.

### 缓存雪崩

> 解决方案

自行百度……