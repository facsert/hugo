---
title: Linux 配置
description: 
date: 2022-08-09 21:20:45
categories:
    - Linux 教程
tags:
    - Linux
---

## 网络配置

Debian 网络配置

```bash
 $ vi /etc/network/interfaces

 auto enp125s0f0                                 # 网卡名
 iface enp125s0f0 inet static                    # 设置静态 IP
 address 10.58.14.96/24                          # 设置 IP 地址和掩码
 gateway 10.10.10.1                              # 设置网关

 auto enp125s0f0                                 # 网卡名称
 iface enp125s0f0 inet dhcp                      # 设置动态 IP
 address 10.58.14.96/24                          # 设置 IP 地址和子网掩码
 gateway 10.10.10.1                              # 设置网关

 $ systemctl restart networking                  # 重启网络服务
```

Centos

```bash
 vi /etc/sysconfig/network-scripts/ifcfg-eth0

 TYPE=Ethernet                                   # 类型=以太网络
 PROXY_METHOD=none                               # 代理模式
 BROWSER_ONLY=no
 DEFROUTE=yes                                    
 IPV4_FAILURE_FATAL=no
 IPV6INIT=yes                                    # 启用IPV6协议
 IPV6_AUTOCONF=yes                               # 自动配置IPV6地址
 IPV6_DEFROUTE=yes
 IPV6_FAILURE_FATAL=no
 IPV6_ADDR_GEN_MODE=stable-privacy
 NAME=ens32
 UUID=0e6d72a7-8a6c-43ac-aef2-25d165562fd0　　

 DEVICE=eth0                                     # 设备名称
 BOOTPROTO=none                                  # 开机协议 static dhcp none
 ONBOOT=yes                                      # 启动或重启网络时，启动该设备，yes启动，no不启动
 IPADDR=192.168.1.15                             # IPV4 地址
 PREFIX=24                                       # 子网掩码
 GATEWAY=192.168.1.1                             # 网关
 DNS1=192.168.1.2                                # DNS服务器地址
 IPV6_PRIVACY=no                                 # IPV6协议

 $ systemctl restart network                     # 重启网络
```

## 配置 ssh

```bash
 $ vi /etc/ssh/sshd_config                       # 编辑 ssh 系统配置文件

 #PermitRootLogin prohibit-password
 PermitRootLogin yes                             # 允许远程 root 用户登录

 #PasswordAuthentication no
 PasswordAuthentication yes                      # 允许用户密码作为口令验证

 $ systemctl restart sshd                        # 重启 ssh 服务，令配置生效
```

## host 配置

命令行开头 `root@debian:~/#` 

- root: 用户名称
- debian: Linux host 名称
- ~/: 当前目录
- #: root 用户标识(普通用户为 $)

```bash
 $ hostname                                      # 打印本机 hostname
 > debian

 $ hostname -i                                   # 打印本机 IP
 > debian

 $ hostnamectl set-hostname ubuntu               # 修改 hostname, 立即生效
 $ hostname
 > ubuntu
```

通过 `/etc/hosts` 文件可以将主机名与 IP 绑定快速解析

```bash
 $ vi /etc/hosts                                 # 域名解析为 IP 地址的配置文件

 127.0.0.1        localhost                      # localhost 会被解析为 IP 127.0.0.1
 192.168.1.49     node                           # node 被解析为对应 IP, 例如 ping node == ping 192.168.1.49
 123.123.123.123  baidu                           
```

通过 `/etc/resolve.conf` 指定 DNS 解析的服务器地址
用户通过域名访问网站, 请求发送到 DNS 服务器, DNS 服务器将域名解析为具体的 IP 返回给用户机器

```bash
 $ vi /etc/resolve.conf

 nameserver  8.8.8.8                             # 添加 DNS 解析服务器 IP 地址
 nameserver  10.10.10                            # 添加 DNS 解析服务器 IP 地址

 $ source /etc/resolve.conf                      # 配置生效
```

```bash
      www.baidu.com     123.123.123.123    123.123.123.123
computer -------> DNS server -------> computer -------> baidu server

```

## Windows WSL ssh 连接

WSL 下查看 ssh 服务端口

```bash
 $ ss -ntlp | grep ssh                           # Debian Ubuntu 默认端口查看工具
 > LISTEN   0   128   0.0.0.0:2222   0.0.0.0:*  users:(("sshd",pid=4628fd=4))
 > LISTEN   0   128      [::]:2222      [::]:*  users:(("sshd",pid=4628,fd=3))

 $ netstat -ntlp | grep ssh                      # Redhat Centos netstat 查看端口
 > tcp    0   0 0.0.0.0:2222   0.0.0.0:*   LISTEN   4628/sshd: /usr/sbi 
 > tcp6   0   0 :::2222        :::*        LISTEN   4628/sshd: /usr/sbi 

 $ systemclt start sshd                          # 启动 ssh 服务
 $ service ssh start
```

Windows > 设置 > 应用 > 可选功能 > 添加可选功能  
选择 OpenSSH 服务端和 OpenSSH 服务端安装  
安装后在可选功能界面下方检查是否安装成功  

Windows 打开终端管理员, 设置 WSL IP 和 ssh 端口映射到 Windows 端口

```powershell
netsh interface portproxy add v4tov4 listenport=2222 listenaddress=0.0.0.0 connectport=2222 connectaddress=172.28.185.15

# listenport windows 端口
# listenaddress windows IP
# connectport WSL 端口
# connectaddress WSL IP

# 查看端口映射列表
netsh interface portproxy show all
侦听 ipv4:                 连接到 ipv4:

地址            端口        地址            端口
--------------- ----------  --------------- ----------
0.0.0.0         2222        172.28.185.15   2222
```
