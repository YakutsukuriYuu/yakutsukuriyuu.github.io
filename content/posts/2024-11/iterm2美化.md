+++
title = '终端美化和命令行加强'
date = '2024-11-24T18:32:16+08:00'
draft = false
categories = ["教程"]
tags = ["iterm2", "命令行加强"]
author ='Yuu'
+++

## 准备阶段 

需要homebrew包管理器

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装iterm2

```shell
brew install iterm2
```

## 下载hack nerd字体

```shell
git clone https://github.com/ryanoasis/nerd-fonts.git --depth 1
cd nerd-fonts
./install.sh
```

### iterm2 设置默认主题

![](https://raw.githubusercontent.com/YakutsukuriYuu/picGo/main/hexo/%E6%88%AA%E5%B1%8F2024-06-02%2007.12.22.png)

### iterm2配置hack nerd字体

![](https://raw.githubusercontent.com/YakutsukuriYuu/picGo/main/hexo/setting1.png)

![](https://raw.githubusercontent.com/YakutsukuriYuu/picGo/main/hexo/setting2.png)

图片来自https://blog.csdn.net/SmallTeddy/article/details/124850597 侵删

## 安装oh-my-zsh

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

## 安装主题powerlevel10k

```shell
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

在***.zshrc***中设置主题***ZSH_THEME="powerlevel10k/powerlevel10k"***

## 安装插件

[**zsh-completions**](https://github.com/zsh-users/zsh-completions)

```shell
  git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions
```

[**zsh-autosuggestions**](https://github.com/zsh-users/zsh-autosuggestions)

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

[**zsh-syntax-highlighting**](https://github.com/zsh-users/zsh-syntax-highlighting)

```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

加入到***.zshrc***中

```shell	
plugins=(
git
zsh-completions 
zsh-autosuggestions 
zsh-syntax-highlighting
)
```





全部完整的的***.zshrc***代码

```shell
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

export ZSH="$HOME/.oh-my-zsh"

ZSH_THEME="powerlevel10k/powerlevel10k"

plugins=(
git
zsh-completions 
zsh-autosuggestions 
zsh-syntax-highlighting
)
source $ZSH/oh-my-zsh.sh

# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh

# auto插件会有点bug 加上这个处理。
unset ZSH_AUTOSUGGEST_USE_ASYNC

```

iterm2界面125*35

配色方案github Dark

[iterm2 配色网站](https://iterm2colorschemes.com/)

## 配置powerlevel10k

命令行输入 ***p10k configure***

我们设置了字体 这里直接***n***就行

![](https://raw.githubusercontent.com/YakutsukuriYuu/picGo/main/hexo/%E6%88%AA%E5%B1%8F2024-06-02%2007.06.42.png)

这里选rainbow

![](https://raw.githubusercontent.com/YakutsukuriYuu/picGo/main/hexo/%E6%88%AA%E5%B1%8F2024-06-02%2007.07.16.png)

***最重要的就是这里***，选择第一项

![](https://raw.githubusercontent.com/YakutsukuriYuu/picGo/main/hexo/%E6%88%AA%E5%B1%8F2024-06-02%2007.07.25.png)

这样子才会有状态栏

![](https://raw.githubusercontent.com/YakutsukuriYuu/picGo/main/hexo/%E6%88%AA%E5%B1%8F2024-06-02%2007.11.07.png)

# 命令行增强工具

## lsd 增强形态的ls

```shell
brew install lsd
```

## bat

```shell
brew install bat
```

## diff-so-fancy

```shell
brew install diff-so-fancy
```

## htop

```shell
brew install htop
```

## prettyping

```shell
brew install prettyping
```

### fzf 强大的文件模糊搜索工具

```shell
brew install fzf
```

## .zshrc 配置

```shell
alias ls=lsd
alias cat=bat
alias diff=diff-so-fancy
alias top=htop
alias ping=prettyping
```



## 我完整的***.zshrc***

```shell
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/opt/anaconda3/bin/conda' 'shell.zsh' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/opt/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/opt/anaconda3/etc/profile.d/conda.sh"
    else
        export PATH="/opt/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<

if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

export ZSH="$HOME/.oh-my-zsh"

ZSH_THEME="powerlevel10k/powerlevel10k"

plugins=(
git
zsh-completions 
zsh-autosuggestions 
zsh-syntax-highlighting
)
source $ZSH/oh-my-zsh.sh

# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh

# auto插件会有点bug 加上这个处理。
unset ZSH_AUTOSUGGEST_USE_ASYNC

alias ls=lsd
alias cat=bat
alias diff=diff-so-fancy
alias top=htop
alias ping=prettyping
alias cl=clear

```

