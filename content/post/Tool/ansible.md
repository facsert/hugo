---
title: Ansible
description: 
date: 2022-11-08 16:03:37
categories:
    - 工具
tags:
    - Ansible
---

## 介绍

`ansible` 是一个基于 `python` 实现的自动化运维工具, 实现对多个系统进行批量化操作.



## 环境


安装


```bash
 $ apt install ansible 
```


### 配置节点


编辑 `/etc/ansible/hosts` 添加群组或节点

```bash
[manager]
localhost

[node]
172.17.0.10

```


## 命令


```bash
 $ ansible
 $ ansible-doc
 $ ansible-playbook
 $ ansible-vault
 $ ansible-console

 $ ansible <host pattern> -m <module> -a <args>
 $ ansilbe all -m shell -a "ifconfig"
```


### 查询

```bash
 $ ansible --version
 $ ansible all --list-host
 $ 

```

## 执行命令

```bash
 $ ansible <host pattern> -m command -a <command>
 $ ansible <host pattern> -m shell -a <shell command>
```

### 文件


```bash
 $ ansible node -m copy -a "src=/root/local dst=/root/remote"
 $ ansible node -m fetch -a "src=/root/remote dst=/root/local"
```


## Playbook
