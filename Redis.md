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
127.0.0.1:6379> keys *  #查看所有的key
1) "name"
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

