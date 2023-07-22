# Git Remote repository

## 介绍

远程仓库是托管在网上的项目版本库, 需要在代码平台上创建, 常见的如

- [Github](https://github.com/)
- [Gitlab](https://about.gitlab.com/)
- [Gitee](https://gitee.com/)

可以通过两种方式添加远程仓库

```bash
 $ git clone <Repository url>                    # 克隆远端仓库到本地, 自动绑定链接对应的仓库

 $ git remote -v                                 # 查看本地仓库关联的所有远端仓库
 > origin  git@github.com:facser/Learning.git (fetch)
 > origin  git@github.com:facser/Learning.git (push)

 $ git remote add origin <Repository url>        # 将远程仓库命名为 origin 并关联本地仓库
```

- 一个本地仓库可以关联多个远程仓库
- 本地仓库可以提交代码到任意一个已关联的远程仓库
- 使用克隆后, 该远程仓库默认被命名为 origin

## .gitignore

忽略指定文件, 不对其追踪和提交, 文件名固定为 `.gitignore`, 项目内可创建**多个**

```bash
 $ cat .gitignore
 > *.pyc                                         # 忽略当前项目内所有 .pyc 结尾的文件
 > !main.pyc                                     # 强制跟踪所有 main.pyc
 > /*.log                                        # 忽略当前目录下 .log 结尾文件, 不影响上层的文件
 > temp/                                         # 忽略整个项目内所有 temp 文件夹
 > /lib/*.pyc                                    # 忽略当前 lib 目录 下一级的 pyc 文件, lib 下多层目录不受影响  
 > lib/**/.pyc                                   # 忽略项目内所有 lib 文件夹内的 pyc 文件
```

## SSH key 密钥

本地仓库推送代码到远端时, git 会要求用户输入用户名和密码, 设置 ssh key 即可**免密码**推送

### 生成密钥

|密钥|linux|windows|
|:--:|:--:|:-:|
|id_rsa (私钥)|`/root/.ssh/id_rsa`|`C:\Users\<user>\.ssh`|
|id_rsa.pub (公钥)|`/root/.ssh/id_rsa.pub`|`C:\Users\<user>\.ssh`|

生成两个密钥文件, 将**公钥**放到代码托管平台的账号设置中

```bash
 $ ssh-keygen                                    # 自动生成密钥
 $ ssh-keygen -t rsa -C "key message"            # 生成 rsa 类型带 key 注释信息的密钥
 
 $ cat ~/.ssh/id_rsa.pub                         # 公钥以 ssh-rsa 开头, 注释信息结尾
 > ssh-rsa xxx....xxx key message
```

- 密钥由系统生成, 放入用户设置, 所以是系统和账户绑定
- 系统下的本地仓库推送代码到该账户使用 ssh 链接时即可免密