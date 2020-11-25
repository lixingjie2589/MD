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

