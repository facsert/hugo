---
title: Bash 控制流
description: 
date: 2022-08-21 22:24:10
categories:
    - Bash 教程
tags:
    - Bash
---

## 条件分支

### if 分支

```bash
if <command> ; then                             # command 返回值为 0 表示条件成立
commands
fi

$ if date; then echo "true"; fi                 # 命令执行成功表示条件成立
> Mon Aug 15 21:03:03 CST 2022                  # date 命令执行成功
> true                                          # 执行分支代码
```

注: if 分支后的命令或表达式返回 0 表示 true, 条件成立, 其余值均表示 false 不成立

### 多重分支

```bash
if <command>; then                              # command 返回值为 0 表示条件成立
    commands
elif <command>; then                            
    commands
else
    commands
fi

if true; then                                   # 命令执行成功表示条件成立
    echo "first if"; 
elif true; then                                 # 上个 if 条件不成立才会执行判断
    echo "else if";
else                                            # 上述分支均不成立才会执行
    echo "else"; 
fi       

> first if                                      # 执行第一个成立的 if 条件
```

### 分支表达式

```bash
test < expr >                                    # 常用作 if 判断的表达式, 与 [ expr] 等价
[ expr ]                                         # 同上, 与 test 等价, 括号内空格是必须的
[[ expr ]]                                       # 较上述额外支持正则, 括号内空格是必须的
```

注: 单括号需要注意变量引用为空导致命令报错或逻辑错误

#### 数值比较

```bash
[ 3 -eq 3 ]                                   # equal 数值相等表示 true
[ 3 -ne 3 ]                                   # not equal 数值不相等表示 true
 
[ 3 -lt 3 ]                                   # less than 小于
[ 3 -le 3 ]                                   # less equal 小于等于 

[ 3 -gt 3 ]                                   # greater than 大于
[ 3 -ge 3 ]                                   # greater equal 大于等于
```

注: 数值比较不要使用 `>` `<` `==`等符号, 否则会产生逻辑错误

#### 字符串比较

```bash
[ -z "str" ]                                  # zero 字符串长度为 0 为 true
[ -n "str" ]                                  # not zero 字符串长度不为 0 为 true

[ "abc" == "abc" ]                            # 字符串相等为 true
[ "abc" != "abc" ]                            # 字符串不相等为 true
```

#### 文件比较

```bash
[ -e file ]                                   # exist 文件存在为 true
[ -d file ]                                   # dir 文件存在, 且是目录为 true
```

#### 正则判断

```bash
[[ expression]]                               # 仅双括号支持正则表达式

[[ "abc" =~ "b" ]]                            # true 判断 abc 是否包含 b 
[[ "01:01:01" =~ "([0-9]{2}\:){2}[0-9]{2}" ]] # true 正则匹配时间格式
```

#### 逻辑判断

```bash
[ ! expression ]                              # 单括号逻辑非
[[ ! expression ]]                            # 双括号逻辑非

[ true -a true ]                              # true 单括号逻辑与
[[ true && false ]]                           # false 双括号逻辑与

[ false -o true ]                             # true 单括号逻辑或
[[ false || true ]]                           # true 双括号逻辑或
```

### 逻辑运算

#### 逻辑与

```bash
 $ command && command                            # 前一条命令成功才会执行后一条

 $ true && echo "true"                           # true 返回值 0, 继续执行
 > true

! false && echo "not false"                   # 前一条命令返回值 0, 继续执行
 > not false
```

#### 逻辑或

```bash
 $ command || command                            # 前一条失败后才会继续执行后一条

 $ false || echo "false"                         # false 返回值 1, 执行下一条
 > false
```