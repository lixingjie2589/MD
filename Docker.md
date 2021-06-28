# Docker learning

## 安装

https://docs.docker.com/engine/install/centos/

```bash
## 1.卸载旧版本
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
## 2.安装相关的包
yum install -y yum-utils
## 3.设置镜像仓库
yum-config-manager \
    --add-repo \
   https://download.docker.com/linux/centos/docker-ce.repo # 国外
yum-config-manager \
    --add-repo \
   http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo # 国内
   
## 更新yum软件包
yum makecache fast
## 安装docker相关
yum install docker-ce docker-ce-cli containerd.io
## 测试是否安装成功
docker run hello-world
## 默认工作路径
/var/lib/docker
```

## 基本命令

https://docs.docker.com/reference/        参考地址

```bash
# 启动
systemctl start docker
# 查看版本
docker version
# 查看docker信息
docker info
```

## 镜像命令

```bash
# 查看镜像 -a所有镜像 -q只显示镜像id
docker images -aq
# 搜索镜像
docker search
# 下载镜像
docker pull 镜像名[:tag]
# 删除
docker rmi -f id或名称
docker rmi -f $(docker images -aq)
```



## 容器命令





# Docker安装其他软件

## Docker 安装 Nginx

```bash
#创建要挂载的配置目录
mkdir -p /mydata/nginx/conf

#启动临时nginx容器
docker run -p 80:80 --name nginx -d nginx:1.10

#拷贝出 Nginx 容器的配置
# 将nginx容器中的nginx目录复制到本机的/mydata/nginx/conf目录
docker container cp nginx:/etc/nginx /mydata/nginx/conf

# 复制的是nginx目录，将该目录的所有文件移动到 conf 目录
mv /mydata/nginx/conf/nginx/* /mydata/nginx/conf/

# 删除多余的 /mydata/nginx/conf/nginx目录
rm -rf /mydata/nginx/conf/nginx

#删除临时nginx容器
# 停止运行 nginx 容器
docker stop nginx
# 删除 nginx 容器
docker rm nginx

#启动 nginx 容器
docker run -p 80:80 --name nginx \
-v /mydata/nginx/html:/usr/share/nginx/html \
-v /mydata/nginx/logs:/var/log/nginx \
-v /mydata/nginx/conf/:/etc/nginx \
-d nginx:1.10

#设置 nginx 随 Docker 启动
docker update nginx --restart=always
```

## Docker install redis

```bash
#docker拉取redis镜像
docker pull redis

#创建redis配置文件目录
mkdir -p /mydata/redis/conf
touch /mydata/redis/conf/redis.conf

#启动redis容器
docker run -p 6379:6379 --name redis \
-v /mydata/redis/data:/data \
-v /mydata/redis/conf/redis.conf:/etc/redis/redis.conf \
-d redis redis-server /etc/redis/redis.conf #以指定配置文件启动

#配置
echo "appendonly yes"  >> /mydata/redis/conf/redis.conf

# 重启生效
docker restart redis

#容器随docker启动自动运行
docker update redis --restart=always
```

## Docker install mysql

```bash
#拉取
docker pull mysql:5.7

#docker启动mysql
docker run -p 3306:3306 --name mysql \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7

#容器随docker启动自动运行
docker update mysql --restart=always
```

