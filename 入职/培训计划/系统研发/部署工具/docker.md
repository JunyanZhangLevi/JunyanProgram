# README

- docker install offline
- download url:`https://download.docker.com/linux/static/stable/x86_64/`

## 1. Docker 概念

- 1. 镜像：镜像类似虚拟机中的只读模板，属于一个独立的文件系统，带有创建 Docker 容器的指令。通常，一个镜像是基于另一个镜像的，还需要进行一些额外的定制。例如，您可以构建一个基于 ubuntu 镜像的镜像，但是安装 Apache web 服务器和您的应用程序，以及使您的应用程序运行所需的配置细节。
- 2. 容器：容器是用镜像创建的运行实例。每个容器都可以被启动，开始，停止，删除，同时容器之间相互隔离，保证应用运行期间的安全。我们可以把容器理解为一个精简版的 linux 操作系统。服务在容器中运行。

## 2. Docker 安装

```sh
#上传到Centos系统/data/目录,如
scp -r docker root@192.168.0.5:/data/appBins/basic/

#进入data目录,解压docker包
cd /data/appBins/basic/docker
tar -zxvf docker-20.10.17.tgz

#将解压出来的docker文件内容移动到 /usr/bin/ 目录下
cp docker/* /usr/bin/

#查看docker版本
docker version

#查看docker信息
docker info
```

## 3. 配置 Docker 开机自启动服务

```sh
#添加docker.service文件
vi /etc/systemd/system/docker.service
#按i插入模式,复制如下内容:
```

```conf
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
# restart the docker process if it exits prematurely
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target
```

```sh
#添加文件可执行权限
chmod +x /etc/systemd/system/docker.service

#重新加载配置文件
systemctl daemon-reload

#启动Docker
systemctl start docker

#查看docker启动状态
systemctl status docker

#查看启动容器
docker ps

#设置开机自启动
systemctl enable docker.service

#查看docker开机启动状态 enabled:开启, disabled:关闭
systemctl is-enabled docker.service
```

## 4. 导入镜像

```sh
#docker
cd /data/appBins/basic/docker
#解压
unzip images.zip
#进入images目录
cd images

#导入镜像文件
docker load < images-ui.tar
docker load < images-springboot.tar

#查询所有镜像
docker images
```

## 5. 导出镜像

```sh
docker save -o images-ui.tar spring/ui
```

## 6. docker-compose

### 6.1. 安装

1. 下载安装包:`https://github.com/docker/compose/releases`；
2. 安装过程：

```sh
cd /data/appBins/basic/docker/
# 重命名可执行文件
mv docker-compose-linux-x86_64 docker-compose
# 复制可执行文件到bin目录
cp docker-compose /usr/bin/
# 授权
chmod +x /usr/bin/docker-compose
# 查看安装是否成功
docker-compose --version

# 配置快捷指令compose
vi /etc/profile
## 修改profile内容并保存
alias compose='/usr/bin/docker-compose -f /data/appBins/basic/docker/docker-compose.yml'

# 加载profile
source /etc/profile
```

### 6.2. Network 网络配置

- docker 自动创建的网桥与电信分配的堡垒机 ip 冲突解决方案；
- 原因：docker 自动创建的 bridge 网桥默认使用 172.16.0.0/16，与电信分配给堡垒机的 ip 冲突。多次执行 docker-compose up -d 后，自动生成的网桥会一次使用 172.17.x.x，172.18.x.x；

```sh
# 查看docker版本
docker info | grep 'Server Version'

# 停止docker容器
sudo systemctl stop docker

# 停止docker0网桥
sudo ip link set dev docker0 down

# linux brctl command not found, 安装brctl
yum install bridge-utils -y

# 删除docker0网桥
sudo brctl delbr docker0

# 重置iptables
sudo iptables -t nat -F POSTROUTING

# 编辑daemon.json文件
vi /etc/docker/daemon.json

# 复制以下内容
{
  "debug" : true,
  # 网段配置
  "default-address-pools" : [
    {
      "base" : "192.168.0.0/16",
      "size" : 24
    }
  ],
  # 容器内部日志大小限制
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "50m",
    "max-file": "2"
  }
}


# 重启docker容器
sudo systemctl daemon-reload

sudo systemctl start docker

# 修改compose别名
alias compose='/usr/bin/docker-compose -f /data/appBins/basic/docker/docker-compose-app.yml'

# 启动docker-compose
compose up -d
```

### 6.3. 业务应用的操作

- 基础指令：

| 命令                                             | 用途                                                         |
| ------------------------------------------------ | ------------------------------------------------------------ |
| compose ps                                       | 列举应用启动的 Docker 容器                                   |
| compose top                                      | 列举应用启动的进程                                           |
| compose stop #service#                           | 停止正在运行的，**指定的**应用                               |
| compose start #service#                          | 启动处于停止状态中的，**指定的**应用                         |
| compose rm #service#                             | 处于停止状态中的，**指定的**应用，删除其 Docker 容器         |
| compose restart #service#                        | 重启应用                                                     |
| compose down                                     | 停止正在运行的**全部**应用，并删除为应用创建的 Docker 容器。 |
| compose up -d                                    | 新建**全部**应用定义，创建 Docker 容器，并启动应用。         |
| compose logs -f --tail #最近 n 条日志# #service# | 查询**指定的**应用日志，包含所有容器日志                     |

- 首次部署：

```sh
compose up -d
```

- 更新单个服务：

```sh
# 替换发布包和配置文件后执行
compose restart #service#
```

## 7. bash-completion

- 用于补全 bash 指令名和指令参数；
- 安装过程：

```sh
# yum安装
yum install -y bash-completion

# 复制docker和docker-compose补全配置文件
cp /data/appBins/basic/docker/bash-completion/* /usr/share/bash-completion/completions/

# 加载配置
source /usr/share/bash-completion/completions/docker
source /usr/share/bash-completion/completions/docker-compose
source /usr/share/bash-completion/bash_completion
```
