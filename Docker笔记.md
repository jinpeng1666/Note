# 安装

> [!NOTE]
>
> 安装详情，请查询官方文档
>
> [CentOS | Docker Docs](https://docs.docker.com/engine/install/centos/)

### 卸载旧版

```shell
yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine \
    docker-selinux 
```

### 配置Docker的yum库

首先要安装一个yum工具

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

> [!WARNING]
>
> 报错：
>
> ![image-20250307191215568](https://raw.githubusercontent.com/jinpeng1666/picgo/master/Typora/other/image-20250307191215568.png)
>
> 方法：
>
> [《CentOS 7 镜像源失效终极解决方案（2024年更新）》——生命周期终止后的镜像修复与替代方案_解决centos镜像源不可用导致的下载安装软件失败的通病 原-CSDN博客](https://blog.csdn.net/lijie0213/article/details/145797105)
>
> ```shell
> # 进入仓库配置目录
> cd /etc/yum.repos.d/
>  
> # 备份原有配置
> sudo mkdir backup && sudo mv CentOS-* backup/
>  
> # 下载阿里云镜像配置
> sudo curl -O http://mirrors.aliyun.com/repo/Centos-7.repo
>  
> # 重建缓存
> sudo yum clean all && sudo yum makecache
> ```

安装成功后，执行命令，配置Docker的yum源（已更新为阿里云源）：

```shell
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

sudo sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
```

更新yum，建立缓存：

```shell
sudo yum makecache fast
```

### 安装Docker

```shell
yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 启动和校验

```shell
# 启动Docker
systemctl start docker

# 停止Docker
systemctl stop docker

# 重启
systemctl restart docker

# 设置开机自启
systemctl enable docker

# 执行docker ps命令，如果不报错，说明安装启动成功
docker ps
```

