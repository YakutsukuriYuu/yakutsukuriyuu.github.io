+++
title = 'win11下的深度学习环境搭建(wsl2)'
date = '2025-05-04T04:42:16+08:00'
draft = false
categories = ["深度学习"]
tags = ["深度学习"]
author ='Yuu'

+++

### 打开 PowerShell（以管理员身份），执行：

```powershell
wsl --list --online
```

查看可以安装的发行版

```powershell
PS C:\Users\Yuu> wsl --list --online
以下是可安装的有效分发的列表。
使用 'wsl.exe --install <Distro>' 安装。

NAME                            FRIENDLY NAME
AlmaLinux-8                     AlmaLinux OS 8
AlmaLinux-9                     AlmaLinux OS 9
AlmaLinux-Kitten-10             AlmaLinux OS Kitten 10
Debian                          Debian GNU/Linux
FedoraLinux-42                  Fedora Linux 42
SUSE-Linux-Enterprise-15-SP5    SUSE Linux Enterprise 15 SP5
SUSE-Linux-Enterprise-15-SP6    SUSE Linux Enterprise 15 SP6
Ubuntu                          Ubuntu
Ubuntu-24.04                    Ubuntu 24.04 LTS
archlinux                       Arch Linux
kali-linux                      Kali Linux Rolling
openSUSE-Tumbleweed             openSUSE Tumbleweed
openSUSE-Leap-15.6              openSUSE Leap 15.6
Ubuntu-18.04                    Ubuntu 18.04 LTS
Ubuntu-20.04                    Ubuntu 20.04 LTS
Ubuntu-22.04                    Ubuntu 22.04 LTS
OracleLinux_7_9                 Oracle Linux 7.9
OracleLinux_8_7                 Oracle Linux 8.7
OracleLinux_9_1                 Oracle Linux 9.1
```
### 安装debian发行版

```powershell
wsl --install Debian
```
### 安装miniconda3

下载安装脚本(x86)

```bash
cd ~
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

都是yes接受,默认安装路径.

刷新环境变量,验证安装.

```bash
source ~/.bashrc
# 或者是zsh
source ~/.zshrc
```

验证

```bash
conda --version
```

### 更换软件源

#### 系统软件源

1.备份原有软件源

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

2.创建新的软件源

```bash
sudo nano /etc/apt/sources.list
```

3.更换中科大软件源

```bash
# 中科大源
deb http://mirrors.ustc.edu.cn/debian/ stable main contrib non-free non-free-firmware
deb http://mirrors.ustc.edu.cn/debian/ stable-updates main contrib non-free non-free-firmware
deb http://mirrors.ustc.edu.cn/debian/ stable-backports main contrib non-free non-free-firmware
deb http://mirrors.ustc.edu.cn/debian-security stable-security main contrib non-free non-free-firmware
```

4.更新系统

```bash
sudo apt update && sudo apt upgrade -y
```

#### conda更换软件源

```bash
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge
conda config --set show_channel_urls yes
```

查看是否成功

```bash
conda config --show
```

#### pip更换软件源

创建或者编辑配置文件

```bash
nano ~/.pip/pip.conf
```

添加下面的内容:

```bash
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

没有这个路径可以自行创建.

其他源

| 镜像   | 地址                                     |
| ------ | ---------------------------------------- |
| 清华   | https://pypi.tuna.tsinghua.edu.cn/simple |
| 中科大 | https://pypi.mirrors.ustc.edu.cn/simple  |
| 阿里云 | https://mirrors.aliyun.com/pypi/simple   |

### 安装深度学习的基础环境

创建虚拟环境

```bash
conda create --name d2l python=3.9 -y
```

激活环境

```bash
conda activate d2l
```

安装框架

```bash
pip install torch==1.12.0
pip install torchvision=0.13.0
```

安装d2l软件包,是花书经常使用的函数和类

```bash
pip install d2l==0.17.6
```

### 下载D2L Notebook

```bash
mkdir d2l-zh && cd d2l-zh
curl https://zh-v2.d2l.ai/d2l-zh-2.0.0.zip -o d2l-zh.zip
unzip d2l-zh.zip && rm d2l-zh.zip
cd pytorch
```

在这个文件夹下使用notebook

```bash
jupyter notebook
```

