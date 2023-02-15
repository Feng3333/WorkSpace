# Docker
## 目录
 - [Docker的安装](#docker的安装)
 - [Docker镜像的使用](#docker镜像的使用)
 - [创建镜像](#创建镜像)

## Docker的安装
####  Ubuntu Docker
- 使用官方安装脚本自动安装
```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```
- 也可以使用国内 daocloud 一键安装命令：
```
curl -sSL https://get.daocloud.io/docker | sh
```
#### Windows Docker
https://www.runoob.com/docker/windows-docker-install.html

## Docker镜像的使用
#### 显示本地主机所有的镜像：
```
docker iamges
```
#### 运行某个镜像：
```
docker run --rm -t -i imageName:tag /bin/bash
```
参数说明：  
- -t : 终端  
- -i : 交互式操作  
- imageName : 以该镜像为基础启动容器  
- tag : 镜像版本，如果没有，则默认镜像的tag为latest版本  
- /bin/bash : 放在镜像后的命令，进入交互式shell  
- --rm ： 退出该镜像后会自动删除容器

#### 获取新的镜像
```
docker pull imageName:tag
//举例：
docker pull ubuntu:13.10
```
#### 查找镜像
```
docker search httpd
```
#### 删除镜像
```
docker rmi iamgeName
// 也可以利用id删除
docker rmi iamgeId
```

## 创建镜像
