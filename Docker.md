## Docker-Study

### 安装docker

系统：centos8

- 确保yum包更新到最新

```shell
yum update	
```

- 卸载旧版本

```shell
yum remove docker \
docker-client \
docker-client-latest \
docker-common \
docker-latest \
docker-latest-logrotate \
docker-logrotate \
docker-selinux \
docker-engine-selinux \
docker-engine

rm -rf /etc/systemd/system/docker.service.d
rm -rf /var/lib/docker
rm -rf /var/run/docker
```

```shell
# 查询安装docker的相关的镜像
yum list install | grep docker
```

![](https://raw.githubusercontent.com/1773214022/picture-store/master/img/20210927210135.png)

```shell
#删除相关的镜像 -y参数代表后续的y/n 都选y
yum remove dockerxxx  -y
```

```shell
#删除镜像/容器等
rm -rf /var/lib/docker
```

- 安装需要的软件包，yum-util提供yum-config-manager功能，另外两个是devicemapper驱动依赖

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2
```

- 设置yum源地址（使用阿里云地址）

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

- 查看仓库中的docker版本，请选择特定版本安装

```shell
yum list docker-ce --showduplicates | sort -r
```

- 安装docker

```shell
#指定版本docker
yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io 
```

- 选择最新docker安装

```shell
yum install docker-ce docker-ce-cli containerd.io -y
```

- 启动并加入开机启动

```shell
systemctl start docker   #启动
systemctl stop docker    #停止
systemctl restart docker #重启
systemctl status docker #查看状态
systemctl enabled docker #开机自启动docker
docker -v || docker version 查看docker版本
```

- 卸载Docker

```shell
yum remove docker-ce docker-ce-cli container.io

rm -rf /var/lib/docker
```

### 设置docker镜像源

- Docker镜像服务器在国外，国内访问慢，所以设置国内源
- 阿里镜像源获取：https://cr.console.aliyun.com/cn-shanghai/instances/mirrors

**示例**

```shell
#修改文件
vi /etc/docker/daemon.json

#增加如下配置
{
"registry-mirrors": ["https://u7hiucvq.mirror.aliyuncs.com"],
"dns": ["8.8.8.8","8.8.4.4"]
}

#重启daemon
systemctl daemon-reload

#重启docker
systemctl restart docker.servcie

```

### 常用命令

```shell
#查看运行中的容器
docker ps

#查看所有的容器
docker ps -a

#搜索镜像
docker search keyword
eg：docker search mysql

#查看下载的镜像
docker images

#打包镜像 -t表示指定的镜像库名称/镜像名称:镜像标签 .表示使用当前目录下的DockerFile文件
docker build -t mall/mall-admin:1.0-SNAPSHOT .

#推送镜像
docker login （登录docker hub）

docker tag mall/mall:admin:1.0-SNAPSHOT xlq/mall-admin:1.0-SNAPSHOT(给本地镜像打标签为远程仓库名称)

docker push xlq/mall-admin:1.0-SNAPSHOT （推送到自己远程仓库）
#启动容器
docker start [容器名/容器ID]

#停止容器
docker stop/kill [容器名/容器ID]

#重启容器
docker restart [容器名/容器ID]

#删除已停止的容器
docker rm [容器名/容器ID]

#强制删除容器
docker rm -f [容器名/容器ID]

#按名称通配符删除容器
docker rm `docker ps -a | grep mall-* | awk'{print $1}'`

#强制删除所有容器
docker rm -f $(docker ps -a -q)

#删除镜像
docker rmi 镜像id

#查看容器日志
docker logs [容器名/容器ID]

#动态查看容器产生的日志
docker logs -f ${ContainerName}

#查看容器运行状态信息
docker stats

#获取容器/镜像的元数据
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
eg: docker inspect --format "{{.State.Pid}}" mysql

#查看容器IP
docker inspect --fromat '{{.NetworkSettings.IPAddress}}'

#修改容器的启动方式
docker container update --restart=always $ContainerName

#查看容器中运行的进程信息，支持ps命令参数
docker top [OPTIONS] CONTAINER[ps OPTIONS]

#将宿主机目录拷贝到容器目录
docker cp [path1] [容器ID]:[path2]

#将容器目录拷贝到宿主机目录
docker cp [容器ID]:[path2] [path1]

#同步宿主机时间到容器
docker cp /etc/localtime $ContainerName:/etc/

#执行容器内部命令
docker exec -it  $CintainerName /bin/bash
```

**创建容器并且运行**

```shell
#创建并且运行容器
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
#常用OPTIONS
-d: 后台运行容器，并且返回容器ID
-P: 指定端口映射  格式：主机port：容器port 
-i: 以交互模式运行容器，通常和-t一起使用
-t: 为容器重新分配一个伪输入终端
-e: 设置环境变量 eg:TZ="Asia/Shanghai"设置时区
--env-file=[]: 从指定文件读入环境变量
-m: 设置容器最大内存
--volume -v :将宿主机上的文件挂载到容器文件目录上 格式 宿主机文件目录：容器文件目录
--name xxx :为容器命名
--privileged=true 获取宿主机root权限
--restart=always 容器自动重启
```

### 常用软件安装

