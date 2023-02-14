# Dockerfile
## 目录
 - [环境介绍](#环境介绍)
 - [指令介绍](#指令介绍)
   - [FROM](#from)
   - [MAINTAINER](#maintainer)
   - [LABEL](#label)
   - [ADD](#add)
   - [COPY](#copy)
   - [EXPOSE](#expose)
   - [ENV](#env)
   - [在Dockerfile中使用变量的方式](#在dockerfile中使用变量的方式)
   - [RUN](#run)
   - [CMD](#cmd)
   - [RUN&&CMD](#runcmd)
   - [ENTRYPOINT](#entrypoint)
   - [VOLUME](#volume)
   - [USER](#user)
   - [WORKDIR](#workdir)
   - [AGR](#agr)
   - [ONBUILD](#onbuild)
   - [STOPSIGNAL](#stopsignal)
   - [HEALTHCHECK](#healthcheck)

## 环境介绍
- 1. Dockerfile中所用到的所有文件一定要和Dockerfile文件在同一级父目录下，可以为Dockerfile父目录的子目录；
- 2. Dockerfile中相对默认路径都是Dockerfile所在的目录
- 3. Dockerfile中每一条指令被视为一层
- 4. Dockerfile中指令一般为大写形式(约定俗称)
- 5. Dockerfile中最好做到惜字如金，能写到一行的指令一定要写到一行，原因是分层结构，联合挂载这个特性

## 指令介绍
### FROM
- 功能为指定基础镜像，并且必须为第一条指令！！  
如果不以任何镜像为基础，那么写法为：FROM scratch  
同时这也意味着接下来所写的指令将作为镜像的第一层开始

语法：
```
FROM <image>
FROM <image>:<tag>
FROM <image>:<digest>
```
三种写法，其中\<tag\>和\<digest\>为可选项，如果没有选择，则默认值为latest


### MAINTAINER
指定作者

语法：
```
MAINTAINER <name>
```
- 新版docker中使用LABEL指明

### LABEL
功能为镜像指定标签

语法：
```
LABEL <key>=<value> <key>=<value> <key>=<value> ...

//一个Dockerfile中可以有多个LABEL，如下：
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates \
that label-values can span multipe lines"
//但是建议不建议这样写，做好就写成一行，如太长需要换行的话可以使用 \ 符号

//如下：
LABEL multi.label1="value1" multi.label2="value2" \
multi.label3="value3" \
other="other value"
```
说明：LABEL会继承基础镜像种的LABEL，如遇到key相同，则其值会被覆盖

### ADD
一个复制命令，把文件复制到镜像中；  
如果把虚拟机和容器想象成两天Linux服务器的话，那么这个命令就类似与scp，只是scp需要用户名和密码的权限认证，而ADD不用。  

语法：
```
ADD <src>....<dest>
ADD ["<src>",..."<dest>"]
```
- 路径的填写可以是容器内的绝对路径，也可以是相对于工作目录的相对路径，推荐写成绝对路径  
- 可以是一个本地文件或者是一个本地压缩文件，还可以是一个url  
- 如果把\<dest\>写成一个url，那么ADD就类似于wget命令  
  示例：
```
ADD test relativeDir/
ADD test /relativeDir
ADD http://example.con/foobar /
```
注意事项：  
- src为一个目录的时候，会自动把目录下的文件全部复制过去，目录本身不会复制
- 如果src为多个文件，dest一定要是一个目录

### COPY 
复制命令  
语法：
```
COPY <src>....<dest>
COPY ["<src>",..."<dest>"]
```
与ADD的区别：
- COPY的只能是本地文件，其他用法一致

### EXPOSE
功能为暴露容器运行时的监听端口给外部  
但是EXPOSE并不会使容器访问主机的端口  
如果想使得容器与主机的端口有映射关系，必须在容器启动的时候加上 -P参数

语法：
```
EXPOSE <port>/<tcp/udp>
```

### ENV
功能为设置环境变量

语法有两种：  
```
EVA <key> <value>
EVA <key>=<value> ...
```
两种的区别就是第一种是一次设置一个，第二种是一次设置多个

### 在Dockerfile中使用变量的方式
```
1. $varname
2. ${varname}
3. ${varname:-default value}
4. ${varname:+default value}
```
解释：  
- 第一种和第二种相同
- 第三种表示当变量 不存在 时使用-号后面的值
- 第四种表示当变量 存在 时使用+号后面的值（当然变量不存在时也是使用后面的值）

### RUN
功能为运行指定的指令

RUN命令有两种指令格式：  
```
RUN <command>
RUN ["executable", "param1", "param2"]
```
第一种后边直接跟shell命令
 - 在Linux操作系统上默认 /bin/sh -c
 - 在windows操作系统上默认 cmd/S/C

第二种是类似与函数调用
 - 可将executable理解为可执行文件，后面两个就是参数

### CMD
功能为容器启动时默认命令或参数

语法有三种写法：
```
CMD ["executable", "param1", "param2"]
CMD ["param1", "param2"]
CMD command param1 param2
```
第三种写法就是shell这种执行方式和写法
第一和第二种其实都是可执行文件加上参数的形式
举例说明:
```
CMD ["sh", "-c", "echo $HOME"]
CMD ["echo", "$HOME"]
```
补充细节：这里面包括参数一定要用双引号("")，不能写成单引号；原因在于参数传递后，docker解析的是一个JSON array  

### RUN&&CMD
- 千万不要把RUN和CMD搞混了
- RUN是构建容器是就运行的命令以及提交运行结果
- CMD是容器启动时执行的命令，在构建时并不运行，构建时仅仅是指定了这个命令到底是什么样子的

### ENTRYPOINT
功能是：容器运行时运行的启动命令

语法：  
```
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2
```
- 第一种就是可执行文件加参数(EXEC调用，可在docker run启动时传递参数)
- 第二种就是写shell(shell执行)

与CMD比较说明（两命令比较像，而且可以配合使用）
1. 相同点：
- 只可以写一条，如果写了多条，那么只有最后一条生效
- 容器启动时才运行，运行时机相同
2. 不同点：
- ENTRYPOINT不会被运行的command覆盖，而CMD则会被覆盖
- 如果我们在Dockerfile中同时写了ENTRYPOINT和CMD，并且CMD指令并不是一个完整的可执行的指令，那么CMD指定的内容将会作为ENTRYPOINT的参数  
如下：
```
FROM ubuntu
ENTRYPOINT ["top", "-b"]
CMD ["-c"]
```
- 如果我们在Dockerfile中同时写了ENTRYPOINT和CMD，并且CMD指令是一个完整的可执行的指令，那么他们两个会互相覆盖，谁在最后谁生效  
如下：
```
FROM ubuntu
ENTRYPOINT ["top", "-b"]
CMD ls -al
```
那么最后将执行 ls -al，而top -b不会执行

### VOLUME
可实现挂载功能，可以将宿主机目录挂载到容器中  
可用专用的文件存储当做Docker容器的数据存储部分  
语法：
```
VOLUME ["/data"]
```
说明：
\["/data"\] 可以是一个JsonArray，也可以是多个值。所以还有如下几种写法：
```
VOLUME ["/var/log"]
VOLUME /var/log
VOLUME /var/log lvar/db
```
一般使用场景为需要持久化存储数据时  
容器使用的是AUFS，这种文件系统不能持久化数据，当容器关闭后，所有的更改都会丢失  
所以当数据需要持久化时使用这个命令。

### USER
设置启动容器的用户，可以使用户名或者UID:
```
USER daemo
USER UID
```
注意：
- 如果设置了容器以daemo这个用户去运行，那么RUN,CMD,ENTRYPOINT 都会以这个用户去运行，
- 使用这个命令一定要确认容器中拥有这个用户，并且拥有足够权限

### WORKDIR
语法：
```
WORKDIR /path/to/workdir
```
功能： 设置功能目录，对RUN,CMD,ENTRYPOINT,COPY,ADD生效，如果不存在则会创建，也可以设置多次  
如:
```
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```
pwd的执行结果是/a/b/c

WORKDIR也可以解析环境变量  
如：
```
ENV DIRPATH /path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
```
pwd的执行结果是 /path/$DIRNAME

### AGR
语法：  
```
AGR <name>[=<default value>]
```
设置命令变量，AGR命令定义了一个变量，在docker build创建镜像的时候，使用 --build-arg = 来指定参数  
如果用户在build镜像时指定了一个参数没有定义在Dockerfile中，那么将会产生warning： \[Warning\] One or more build-args \[foo\] were not consumed.  
可以定义一个或多个参数，如下：  
```
FROM busybox
AGR user1
AGR buildno
```
也可以给参数一个默认值： 
```
FROM busybox
AGR user1=someuser
ARG buildno=1
```
如果给了ARG定义的参数默认值，那么当build镜像是没有指定参数值时，会使用这个默认值  

### ONBUILD
语法：  
```
ONBUILD [INSTRUCTION]
```
这个命令只对当前镜像的子镜像生效。  
比如当前镜像为A ，在Dockerfile中添加：
```
ONBUILD RUN ls -al
```
则这个 ls -al命令不会在镜像A构建或启动时执行  
如果此时有个镜像B是基于A镜像构建的，那么这个ls -al命令会在构建镜像B的时候执行

### STOPSIGNAL
语法：
```
STOPSIGNAL signal
```
功能：STOPSIGNAL命令是当容器停止时给系统发送什么样的指令，默认是15  

### HEALTHCHECK
功能：容器健康状况检查命令  

语法有两种：  
```
HEALTHCHECK [OPTIONS] CMD command
HEALTHCHECK NONE
```
第一个的功能是在容器内部运行一个命令来检查容器的健康状况  
第二个的功能是在基础镜像中取消健康命令检查   
\[OPTIONS\]的选项中支持以下三种选项：   
- -interval=DUTATION 两次检查默认的时间间隔为30秒
- -timeout=DUTATION 健康检查命令运行超时时长，默认30秒
- -retries=N 当连续失败指定次数后，则容器被认为是不健康的，状态为unhealthy，默认次数是3次

注意：  
HEALTHCHECK命令只能出现一次，如果出现了多次，只有最后一个生效  
CMD后边的命令的返回值决定了本次健康检查是否成功，具体返回值如下：  
- 0: success - 表示容器是健康的
- 1: unhealthy - 表示容器已经不能工作了
- 2：reserved - 保留值

举例说明：
```
HEALTHCHECK --interval=5m --timeout=3s \
CMD curl -f http://localhost/ || exit 1
```
健康检查的命令是： curl -f http://localhost/ || exit 1  
两次检查的间隔是5m  
命令超时时间为3秒
