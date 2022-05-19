## 安装Docker

```java
#1、卸载旧版本，官方文档查找 https://docs.docker.com/
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2、设置存储库
# 安装yum-utils包（提供yum-config-manager 实用程序）并设置稳定存储库。
sudo yum install -y yum-utils

3、#设置镜像的仓库，使用国内阿里云镜像
sudo yum-config-manager \
	--add-repo \
	http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
#更新yum软件包索引
yum makecache fast

4、#安装docker相关源 docker-ce 社区 ee 企业版
sudo yum install docker-ce docker-ce-cli containerd.io

5、#启动docker
systemctl start docker

6、#查看docker版本
docker version

7、#运行hello-world
```

![img](../图片/Docker/13b80164dad34d47253cdf7053da2dd4.png)

没找到镜像，pulling向官方远程拉取镜像，签名信息表示已经拉取到

```java
8、#查看hello-world镜像
[root@localhost admin]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   3 months ago   13.3kB

9、#卸载依赖、删除资源
sudo yum remove docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker			#/var/lib/docker	docker默认工作路径
```

