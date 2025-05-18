+++
title = 'Mac下深度学习环境搭建(花书)' 
date = '2025-05-19T04:22:16+08:00' 
draft = false categories = ["深度学习"] 
tags = ["深度学习"] 
author ='Yuu'
+++
## 安装miniconda3
通过打开终端并运行以下命令之一（取决于您的 macOS 体系结构）来下载安装程序：.sh
~~~shell
# apple m系列
curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.sh
~~~
通过运行以下命令进行安装（具体取决于您的 macOS 体系结构）：
~~~shell
bash ~/Miniconda3-latest-MacOSX-arm64.sh
~~~
按返回键查看 Anaconda 的服务条款 （TOS）。然后按住 Return 键滚动。

输入 以同意 TOS。yes

按 Return 键接受默认安装位置 （），或输入其他文件路径以指定备用安装目录。安装可能需要几分钟才能完成。PREFIX=/Users/<USER>/miniconda3

选择初始化选项：

是 - 修改您的 shell 配置，以便在您打开新 shell 时初始化 conda，并自动识别 conda 命令。conda
否 - 不会修改您的 shell 脚本。安装后，如果要初始化，则必须手动进行初始化。有关更多信息，请参阅手动 shell 初始化。conda
安装程序完成并显示“感谢您安装 Miniconda3！

关闭并重新打开终端窗口以使安装完全生效，或使用以下命令刷新终端：
~~~shell
source ~/.zshrc
~~~

初始化完成后会出现类似的代码

~~~shell
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/Users/yuu/miniconda3/bin/conda' 'shell.zsh' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/Users/yuu/miniconda3/etc/profile.d/conda.sh" ]; then
        . "/Users/yuu/miniconda3/etc/profile.d/conda.sh"
    else
        export PATH="/Users/yuu/miniconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
~~~

## 安装深度学习的基础环境



~~~shell
conda create --name d2l python=3.9 -y
conda activate d2l
pip install torch==1.12.0
pip install torchvision==0.13.0
#pip install d2l==0.17.6
#发现这个版本安装不上,直接用最新版
pip install d2l
~~~

## 下载D2L Notebook

~~~shell
mkdir d2l-zh && cd d2l-zh
curl https://zh-v2.d2l.ai/d2l-zh-2.0.0.zip -o d2l-zh.zip
unzip d2l-zh.zip && rm d2l-zh.zip
cd pytorch
~~~

在这个文件夹下使用notebook

~~~shell
jupyter notebook
~~~





