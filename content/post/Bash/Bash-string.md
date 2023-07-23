---
title: Bash 字符串
description: 
date: 2022-08-09 21:20:45
categories:
    - Bash 教程
tags:
    - Bash
---

## 介绍

Bash 所有代码除关键字外默认为字符串

```bash
$ command="date"
$ $command                                       # 取 command 变量值 "date", "date" 作为命令执行
> Mon Jun 12 10:07:40 CST 2023                   # 执行 date, 打印日期

$ echo date                                      # date 被当作字符串, 作为 echo 的参数
> date                                            

$ echo $("date")                                 # 执行 "date", 返回值作为 echo 的参数
> Mon Jun 12 10:23:46 CST 2023
```

Bash 默认第一个字符为指令, 后续值为字符串作为指令的参数

## 字符串操作

### 长度

`${#s}`

```bash
$ s="123 456"; s="456 123"                       # 赋值
$ echo "s:${s}  length: ${#s}"                   # 获取长度
> s:456 123  length: 7                           # 打印
```

### 子字符串

`${s:start:length}`

```bash
$ s="01234"
$ echo "${s:0:3}"                                # 从序号 0 开始, 获取 3 个长度
> 012                                            #
```
