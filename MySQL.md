# MySQL

## MySQL安装

1、下载解压版MySQL文件……

2、配置环境变量；

3、新建MySQL配置文件my.ini

```ini
[mysqld]
port=3306

# mysql根目录
basedir="D:\AppServ\mysql5.7\"

# 放所有数据库的data目录
datadir=D:\AppServ\mysql5.7\data
```

4、在bin目录下cmd初始化数据库

```bash
mysqld -install  # 安装mysql服务

mysqld --initialize-insecure --user=mysql  # 初始化数据文件

net start mysql  #开始mysql服务

mysql -u root –p  #第一次登陆没有密码

#修改密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';

flush privileges  # 刷新权限

net stop mysql  #停止mysql服务

net start mysql  #开始mysql服务
```

5、能连接上就是安装成功；

6、如果装失败

```bash
sc delete mysql # 清空服务，在重新装
```

7、可视化软件自行安装；

## 常用命令

```cmd
mysql> exit;  --退出连接
mysql> show databases;  --查看所有数据库
mysql> use snail;  --切换数据库
mysql> show tables;  -- 查看所有表
mysql> describe student;  -- 显示表的信息
mysql> CREATE DATABASE IF NOT EXISTS db;  -- 创建一个数据库
DROP DATABASE IF EXISTS db;  --删除数据库

SHOW CREATE DATABASE snail;  -- 查看创建数据库语句
SHOW CREATE TABLE student;  -- 查看创建表的语句
DESC student; -- 查看表结构
```

**创建表**

```mysql
CREATE TABLE IF NOT EXISTS `runoob_tbl`(
   `runoob_id` INT UNSIGNED AUTO_INCREMENT COMMENT '注释',
   `runoob_title` VARCHAR(100) NOT NULL,
   `runoob_author` VARCHAR(40) NOT NULL,
   `submission_date` DATE,
   PRIMARY KEY ( `runoob_id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

**修改表**

```mysql
# 添加列
ALTER TABLE table_name ADD column_name datatype
# 删除列
ALTER TABLE table_name DROP COLUMN column_name
# 改变表中列的数据类型
ALTER TABLE table_name ALTER COLUMN column_name datatype
```

## DML(操作数据)

- **insert**

```mysql
# 主键自增可以不用插入
INSERT INTO 表名称 VALUES (值1, 值2,....)

# 指定所要插入数据的列
INSERT INTO table_name (列1, 列2,...) VALUES (值1, 值2,....)
```

- **update**

```mysql
UPDATE 表名称 SET 列名称 = 新值 WHERE 列名称 = 某值
```

- **delete**

```mysql
DELETE FROM 表名称 WHERE 列名称 = 值

# 清空表
TRUNCATE table_name;
```

## DQL(查询数据)

```mysql
# 基本查询
SELECT DISTINCT(去重) 列名称 AS 别名 FROM 表名称 AS 别名
SELECT * FROM 表名称
```

![](image\MySqlImage\20201125.png)

## MySql函数

| COUNT(expression) | 返回查询的记录总数，expression 参数是一个字段或者 * 号 |
| ----------------- | ------------------------------------------------------ |
| SUM(expression)   | 返回指定字段的总和                                     |
| AVG(expression)   | 平均值                                                 |
| MAX(expression)   | 最大值                                                 |
| MIN(expression)   | 最小值                                                 |
| ……                | ……                                                     |

## 数据库MD5加密

```mysql
UPDATE userinfo SET pwd = MD5(pwd)  -- 简单使用
```



## 事务（ACID）

- **原子性：**一个事务要么全部提交成功，要么全部失败回滚，不能只执行其中的一部分操作

- **一致性：**事务前后数据的完整性必须保持一致。

- **隔离性：**

  事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离。

- **持久性：**事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。



**事务控制语句：**

- BEGIN 或 START TRANSACTION 显式地开启一个事务；
- COMMIT 也可以使用 COMMIT WORK，不过二者是等价的。COMMIT 会提交事务，并使已对数据库进行的所有修改成为永久性的；
- ROLLBACK 也可以使用 ROLLBACK WORK，不过二者是等价的。回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；
- SAVEPOINT identifier，SAVEPOINT 允许在事务中创建一个保存点，一个事务中可以有多个 SAVEPOINT；
- RELEASE SAVEPOINT identifier 删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常；
- ROLLBACK TO identifier 把事务回滚到标记点；
- SET TRANSACTION 用来设置事务的隔离级别。InnoDB 存储引擎提供事务的隔离级别有READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ 和 SERIALIZABLE。



**MYSQL 事务处理主要有两种方法：**

1、用 BEGIN, ROLLBACK, COMMIT来实现

- **BEGIN** 开始一个事务
- **ROLLBACK** 事务回滚
- **COMMIT** 事务确认

2、直接用 SET 来改变 MySQL 的自动提交模式:

- **SET AUTOCOMMIT=0** 禁止自动提交
- **SET AUTOCOMMIT=1** 开启自动提交

> 事务测试

```mysql
SET AUTOCOMMIT = 0;  -- 关闭自动提交
START TRANSACTION;  -- 开启一个事务
UPDATE account SET money = money - 500 WHERE `name` = 'A';  -- A-500
UPDATE account SET money = money + 500 WHERE `name` = 'B';  -- B+500
COMMIT;  -- 提交事务
ROLLBACK;  -- 回滚
SET AUTOCOMMIT = 1;  -- 开启自动提交
```

> 代码测试

```mysql
CREATE TABLE account(
	id Int PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(40),
	money FLOAT
);
/*插入测试数据*/
insert into account(name,money) values('A',1000);
insert into account(name,money) values('B',1000);
insert into account(name,money) values('B',1000);
```

测试代码看项目



## 索引

MySQL官方对索引的定义为：索引（index）是帮助MySQL高效获取数据的数据结构（有序）。在数据之外，数据库系统还维护者满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据， 这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。

**一、建立方向索引的不利因素（优点）**

第一，  通过创建唯一性索引，可以保证数据库表中每一行数据的唯一性。
第二，  可以大大加快数据的检索速度，这也是创建索引的最主要的原因。
第三，  可以加速表和表之间的连接，特别是在实现数据的参考完整性方面特别有意义。
第四，  在使用分组和排序子句进行数据检索时，同样可以显著减少查询中分组和排序的时间。
第五，  通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。 

**二、建立方向索引的不利因素（缺点）** 

第一，  创建索引和维护索引要耗费时间，这种时间随着数据量的增加而增加。
第二，  索引需要占物理空间，除了数据表占数据空间之外，每一个索引还要占一定的物理空间，如果要建立聚簇索引，那么需要的空间就会更大。
第三，  当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，这样就降低了数据的维护速度。



**索引分类**

- 普通索引(KEY/INDEX)
  - 默认的，index，key关键字来设置
- 主键索引(PRIMARY KEY)
  - 唯一的标识，主键不可重复，只能有一个列作为主键

- 唯一索引(UNIQUE KEY)
  - 与普通索引相似，不同的是索引列值是唯一的，但可以为null；
- 全文索引(FULLTEXT)
  - 在特定的数据库引擎下才有

**测试**

```mysql
CREATE TABLE `app_user` (
`id` BIGINT(20) UNSIGNED NOT NULL AUTO_INCREMENT,
`name` VARCHAR(50) DEFAULT'' COMMENT'用户昵称',
`email` VARCHAR(50) NOT NULL COMMENT'用户邮箱',
`phone` VARCHAR(20) DEFAULT'' COMMENT'手机号',
`gender` TINYINT(4) UNSIGNED DEFAULT '0'COMMENT '性别（0：男;1:女）',
`password` VARCHAR(100) NOT NULL COMMENT '密码',
`age` TINYINT(4) DEFAULT'0'  COMMENT '年龄',
`create_time` DATETIME DEFAULT CURRENT_TIMESTAMP,
`update_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8 COMMENT = 'app用户表';


-- 插入100万数据.
DELIMITER $$
-- 写函数之前必须要写，标志
CREATE FUNCTION mock_data ()
RETURNS INT
BEGIN
	DECLARE num INT DEFAULT 1000000;
	DECLARE i INT DEFAULT 0;
	WHILE i<num DO
		INSERT INTO `app_user`(`name`,`email`,`phone`,`gender`,`password`,`age`) VALUES(CONCAT('用户',i),'19224305@qq.com',CONCAT('19',FLOOR(RAND()*((999999999-100000000)+100000000))),FLOOR(RAND()*2),UUID(),FLOOR(RAND()*100));
		SET i=i+1;
	END WHILE;
	RETURN i;
END;


SELECT mock_data() -- 执行此函数 生成一百万条数据

set global log_bin_trust_function_creators=TRUE; -- 发生指定错误时使用

-- CREATE INDEX 索引名 ON 表(字段)
CREATE INDEX id_app_user_name ON app_user(`name`)

SELECT * FROM app_user WHERE name = '用户1'
-- 分析
EXPLAIN SELECT * FROM app_user WHERE name = '用户1'
```

**索引数据类型**

……

## 权限用户管理

```mysql
# 创建账号密码
CREATE USER `wangwei`@`127.0.0.1` IDENTIFIED BY 'passowrd';

# 授予权限
GRANT ALL ON *.* TO `wangwei`@`127.0.0.1` WITH GRANT OPTION;

# 删除权限
REVOKE all privileges ON databasename.tablename FROM 'username'@'host';

# 修改密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';

# 重命名 
RENAME USER snail TO snail1;

# 查看权限
SHOW GRANTS FOR root@localhost;

# 删除用户
DROP USER snail1;

```



## 备份

**mysqldump备份命令**

```mysql
# mysqldump -u 用户名 -p 密码 -h 地址 数据库 表 > 物理磁盘位置/文件名
mysqldump -uroot -p -h127.0.0.1 数据库名 student teacher > table-201904301500.sql
```



## 数据库设计规范

### 三大范式

**1．第一范式(确保每列保持原子性)**

第一范式是最基本的范式。如果数据库表中的所有字段值都是不可分解的原子值，就说明该数据库表满足了第一范式。

**2．第二范式(确保表中的每列都和主键相关)**

第二范式在第一范式的基础之上更进一层。第二范式需要确保数据库表中的每一列都和主键相关，而不能只与主键的某一部分相关（主要针对联合主键而言）。也就是说在一个数据库表中，一个表中只能保存一种数据，不可以把多种数据保存在同一张数据库表中。

**3．第三范式(确保每列都和主键列直接相关,而不是间接相关)**

第三范式需要确保数据表中的每一列数据都和主键直接相关，而不能间接相关。

**在商业开发中遵循上面的范式时也需要考虑性能，可能故意增加列……**



### **数据库SQL开发规范**

1. 建议使用预编译语句进行数据库操作。只传参数，比传递sql更加高效，相同语句一次解析之后，多次使用，节约sql解析的成本，提高处理效率。

2. 避免数据类型的隐式转换。隐式转换导致索引失效，一般在where字句条件中出现的类型转换，导致了索引失效。

3. 合理利用已存在索引，而不是盲目添加索引。
避免使用双%的查询条件：like '%123%'，只要出现前缀%，索引失效。
一个SQL只能利用到复合索引的一列进行范围查询，若联合索引 index(a,b,c) 对a进行范围查询，那么b和c将失效，应当将a放到最右侧
使用left join 或 not exists 来优化 not in 操作，not in会使索引失效

4. 程序连接不同数据库时应该使用不同的账号，禁止跨库查询

5. 禁止使用 select * 必须使用 select <字段列表> 查询，消耗过多的IO和cpu以及网络带宽资源

6. 禁止使用不含字段的insert 语句,为了减少表结构的变更带来的影响：insert into table values('a','b','c'); 应当指明要插入的列，insert into table(c1,c2,c3) values('a','b','c');

7. 避免使用子查询，可以将子查询优化为join操作：子查询都会创建临时表，占用cpu和io资源，子查询结果集无法使用索引。

8. 避免使用join关联太多的表：
　　　　每关联一张表，多占用一部分内存（join_buffer_size）
　　　　会产生临时表操作，影响查询效率
MySQL最多允许关联61张表，建议不超过5张表

9. 减少同数据库的交互次数

10. 使用in代替or。in的值不超过500个，in可以有效使用索引，or不行。

11. 禁止使用order by rand() 进行随机排序，这个操作对性能有很大影响，尽量通过程序来得到随机值再从数据库中获取数据。

12. 禁止在where从句中对列进行函数转换和计算，造成索引的失效。where data(createtime) = '2020-11-30' ，尽量在程序中进行计算

13. 在明显不会出现重复值的时候使用union all 而不是union。union会先加载所有数据到临时表中然后去重，而union all不会去重。

14. 拆分复杂的大SQL成多个小SQL。并行执行小SQL来提高处理效率



### 数据库操作行为规范

1. 超过100万行的批量写操作，要分批多次进行操作：

　　　　大批量操作可能造成严重的主从延迟问题

　　　　binlog日志为row格式时，胡产生大量的日志，造成资源不足

　　　　避免产生大事务的操作

2. 对于大表使用pt-online-schema-change工具来修改表结构。过程是：先创建新表，然后复制旧表数据到新表，将新表名称改成旧表名称，最后删除旧表

3. 禁止为程序使用的账号赋予super超管权限

4. 对于程序连接数据库账号，遵循权限最小的原则。程序使用数据库支行和只能在一个DB下使用，不准跨库，程序使用的账号原则上不准有drop权限

以上就是MySQL的一些设计规范，当然不是说一定要遵循以上的原则，具体视实际应用场景而定，通过DBA指导来指定原则。



链接：https://www.jianshu.com/p/cbdf1b3f4e28



## JDBC

### JDBC 架构

分为双层架构和三层架构。

**双层**

![](.\image\MySqlImage\20201130.png)

作用：此架构中，Java Applet 或应用直接访问数据源。

条件：要求 Driver 能与访问的数据库交互。

机制：用户命令传给数据库或其他数据源，随之结果被返回。

部署：数据源可以在另一台机器上，用户通过网络连接，称为 C/S配置（可以是内联网或互联网）。

**三层**

![](.\image\MySqlImage\202011301501.png)

侧架构特殊之处在于，引入中间层服务。

流程：命令和结构都会经过该层。

吸引：可以增加企业数据的访问控制，以及多种类型的更新；另外，也可简化应用的部署，并在多数情况下有性能优势。

历史趋势： 以往，因性能问题，中间层都用 C 或 C++ 编写，随着优化编译器（将 Java 字节码 转为 高效的 特定机器码）和技术的发展，如EJB，Java 开始用于中间层的开发这也让 Java 的优势突显出现出来，使用 Java 作为服务器代码语言，JDBC随之被重视。



### 代码测试

创建测试数据库

```mysql
CREATE DATABASE jdbcStudy CHARACTER SET utf8 COLLATE utf8_general_ci;

USE jdbcStudy;

CREATE TABLE `users`(
	id INT PRIMARY KEY,
	NAME VARCHAR(40),
	PASSWORD VARCHAR(40),
	email VARCHAR(60),
	birthday DATE
);

INSERT INTO `users`(id,NAME,PASSWORD,email,birthday)
VALUES(1,'zhansan','123456','zs@sina.com','1980-12-04'),
(2,'lisi','123456','lisi@sina.com','1981-12-04'),
(3,'wangwu','123456','wangwu@sina.com','1979-12-04')
```

**pom**

```xml
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.21</version>
</dependency>
```

代码在GitHub



## SQL注入

**使用PreparedStatement对象预编译SQL**



## 数据库连接池

### 概述

数据库连接池是负责分配、管理和释放数据库连接，它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个。那么其中的运行机制又是怎样的呢？今天主要介绍一下数据库连接池原理和常用的连接池。

### 为什么要使用连接池

数据库连接是一种关键的有限的昂贵的资源，这一点在多用户的网页应用程序中体现得尤为突出。 一个数据库连接对象均对应一个物理数据库连接，每次操作都打开一个物理连接，使用完都关闭连接，这样造成系统的性能低下。

数据库连接池的解决方案是在应用程序启动时建立足够的数据库连接，并讲这些连接组成一个连接池(简单说：在一个“池”里放了好多半成品的数据库连接对象)，由应用程序动态地对池中的连接进行申请、使用和释放。对于多于连接池中连接数的并发请求，应该在请求队列中排队等待。并且应用程序可以根据池中连接的使用率，动态增加或减少池中的连接数。

　　连接池技术尽可能多地重用了消耗内存地资源，大大节省了内存，提高了服务器地服务效率，能够支持更多的客户服务。通过使用连接池，将大大提高程序运行效率，同时，我们可以通过其自身的管理机制来监视数据库连接的数量、使用情况等。

Java中常用的数据库连接池有：DBCP 、C3P0、BoneCP、Proxool、DDConnectionBroker、DBPool、XAPool、Primrose、SmartPool、MiniConnectionPoolManager及Druid等。

### 传统的连接机制与数据库连接池运行机制区别

#### 不使用连接池流程

不使用数据库连接池的步骤：

1. TCP建立连接的三次握手
2. MySQL认证的三次握手
3. 真正的SQL执行
4. MySQL的关闭
5. TCP的四次握手关闭

可以看到，为了执行一条SQL，却多了非常多网络交互。

优点：

- 实现简单

缺点：

- 网络IO较多
- 数据库的负载较高
- 响应时间较长及QPS较低
- 应用频繁的创建连接和关闭连接，导致临时对象较多，GC频繁
- 在关闭连接后，会出现大量TIME_WAIT 的TCP状态（在2个MSL之后关闭）

#### 使用连接池流程

第一次访问的时候，需要建立连接。 但是之后的访问，均会复用之前创建的连接，直接执行SQL语句。

优点：

- 较少了网络开销
- 系统的性能会有一个实质的提升
- 没了麻烦的TIME_WAIT状态

#### 数据库连接池的工作原理

连接池的工作原理主要由三部分组成，分别为

- 连接池的建立
- 连接池中连接的使用管理
- 连接池的关闭

### 连接池需要注意的点

1、并发问题

为了使连接管理服务具有最大的通用性，必须考虑多线程环境，即并发问题。

这个问题相对比较好解决，因为各个语言自身提供了对并发管理的支持像java,c#等等，使用synchronized(java)lock(C#)关键字即可确保线程是同步的。

2、事务处理

我们知道，事务具有原子性，此时要求对数据库的操作符合“ALL-OR-NOTHING”原则,即对于一组SQL语句要么全做，要么全不做。

我们知道当2个线程共用一个连接Connection对象，而且各自都有自己的事务要处理时候，对于连接池是一个很头疼的问题，因为即使Connection类提供了相应的事务支持，可是我们仍然不能确定那个数据库操作是对应那个事务的，这是由于我们有２个线程都在进行事务操作而引起的。

为此我们可以使用每一个事务独占一个连接来实现，虽然这种方法有点浪费连接池资源但是可以大大降低事务管理的复杂性。

3、连接池的分配与释放

连接池的分配与释放，对系统的性能有很大的影响。合理的分配与释放，可以提高连接的复用度，从而降低建立新连接的开销，同时还可以加快用户的访问速度。

对于连接的管理可使用一个List。即把已经创建的连接都放入List中去统一管理。每当用户请求一个连接时，系统检查这个List中有没有可以分配的连接。如果有就把那个最合适的连接分配给他，如果没有就抛出一个异常给用户。

４、连接池的配置与维护

连接池中到底应该放置多少连接，才能使系统的性能最佳？

系统可采取设置最小连接数（minConnection）和最大连接数（maxConnection）等参数来控制连接池中的连接。比方说，最小连接数是系统启动时连接池所创建的连接数。如果创建过多，则系统启动就慢，但创建后系统的响应速度会很快；如果创建过少，则系统启动的很快，响应起来却慢。这样，可以在开发时，设置较小的最小连接数，开发起来会快，而在系统实际使用时设置较大的，因为这样对访问客户来说速度会快些。最大连接数是连接池中允许连接的最大数目，具体设置多少，要看系统的访问量，可通过软件需求上得到。

如何确保连接池中的最小连接数呢？有动态和静态两种策略。动态即每隔一定时间就对连接池进行检测，如果发现连接数量小于最小连接数，则补充相应数量的新连接,以保证连接池的正常运转。静态是发现空闲连接不够时再去检查。