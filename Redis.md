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