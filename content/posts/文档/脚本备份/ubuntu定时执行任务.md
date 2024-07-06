---
title: ubuntu定时执行任务
categories:
  - 文档
tags:
  - 脚本备份
date: 2024-07-07
updated: 2024-07-07
published: true
---
# 如题
```sh
crontab -e

0 1 * * * /bin/bash ~/create_date_file.sh

```
这行命令表示每天1点（00分 1小时）执行脚本 `~/create_date_file.sh`。
