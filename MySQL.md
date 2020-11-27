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

