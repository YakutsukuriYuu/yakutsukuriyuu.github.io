+++
title = 'Debian12安装docker'
date = '2024-11-24T18:32:16+08:00'
draft = false
categories = ["教程"]
tags = ["debian12", "docker"]
author ='Yuu'
+++
```shell
# 更新软件包索引
sudo apt-get update
 
# 安装需要的软件包以使apt能够通过HTTPS使用仓库
sudo apt-get install ca-certificates curl gnupg lsb-release



# 添加Docker官方的GPG密钥
curl -fsSL https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
 
# 设置稳定版仓库
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null


sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# 验证是否成功安装了docker
sudo systemctl status docker
docker --version

# 修改daemon.json文件，
vim /etc/docker/daemon.json

# daemon.json内容如下：
{
    "registry-mirrors": [
        "https://dockerproxy.com",
        "https://docker.m.daocloud.io",
        "https://cr.console.aliyun.com",
        "https://ccr.ccs.tencentyun.com",
        "https://hub-mirror.c.163.com",
        "https://mirror.baidubce.com",
        "https://docker.nju.edu.cn",
        "https://docker.mirrors.sjtug.sjtu.edu.cn",
        "https://github.com/ustclug/mirrorrequest",
        "https://registry.docker-cn.com"
    ]
}

# 重载配置文件，并重启 docker
sudo systemctl daemon-reload
sudo systemctl restart docker

# 查看 Registry Mirrors 配置是否成功
sudo docker info 
```
## 添加docker用户组
```shell
sudo groupadd docker

sudo usermod -aG docker $USER
# 我发现这样子添加的用户是root 
# 我用的这个(在root用户下)
# usermod -aG docker yakutsukuriyuu  
```

## 安装portainer-ce(可选)
```shell
docker pull portainer/portainer-ce

docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v /dockerData/portainer:/data --restart=always --name portainer portainer/portainer-ce
```
注意：portainer从2.0版本之后更名为portainer/portainer-ce，使用下面新的命令进行拉取最新镜像。带ce的是最新版本，不带ce的已经废弃，停止更新。