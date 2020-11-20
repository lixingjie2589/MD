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