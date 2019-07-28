# homework-linux

## 在linux上部署一个 git 服务

> git 简单易用，为了一探究竟，同时满足好奇心，想深入学习了下如何搭建git服务器。同时会简单说下步骤。

## 系统说明

* 服务器：阿里云 ECS 服务器
* 操作系统：CentOS Linux release 7.5.1804 (Core)
* 相关环境：已安装 git ，熟悉 linux 基本操作。

## 搭建步骤

1、创建一个 ```git``` 用户专门管理 Git 仓库管理，并设置密码

添加 git 这个用户

```
useradd git
```

注：如果权限不够，可加 ```sudo```. 我是直接使用 root 登录，能省点麻烦。

添加用户密码

```
passwd git
```

2、在服务器上选择/创建一个目录作为版本库存地址

因为有了 git 用户，我也想把库的地址放在这个用户下一起，所以是进入 git 用户下文件夹里。

```
cd /home/git/

mkdir -p gitLib
```

3、在这个目录下创建你新建的项目地址，并把项目进行初始化。

```
mkdir -p gittest.git
git init --bare gittest.git
```

4、更改文件权限，更改为git用户

```
chown -R git:git gittest.git/
```

5、测试 clone 代码到本地

```
git clone git@server:/home/git/gitLib/gittest.git
```
说明：```server``` 是你服务器的公网 ip ，```/home/git/gitLib/gittest.git``` 是上面创建的远程仓库路径.

再输入 git 的登录密码就可以 clone 代码。

---

其实现在就已经是可以算是部署完成了，但是涉及到安全性以及每次输入密码都很不爽，所以我们需要禁止 shell 登录，但是仍可以通过 ssh 正常使用 git。

设置ssh免密码登录linux也是比较简单，网上很多教程。[https://www.jianshu.com/p/e9db116fef8c](https://www.jianshu.com/p/e9db116fef8c)

禁止 shell 登录

```
vim /etc/passwd
```

```
git:x:1000:1000::/home/git:/usr/bin/bash
# 修改为
git:x:1000:1000::/home/git:/usr/bin/git-shell
```

注：此处有两个地方需要注意：
1. ssh免密码登录教程大多都不是 git 用户的，可以根据教程把ssh免密码设置在 git 用户目录下 ，比如：```/home/git/.ssh```
2. 禁止 shell 登录，设置git-shell登录。要留意服务器的 ```/usr/bin/``` 是否存在 ```git-shell``` 文件命令，git-shell 随 git 安装到 git 目录下，但是没有配置在```/usr/bin/```下，所以需要使用软链接把 git-shell 配置到 ```/usr/bin/``` ，或者```git:x:1000:1000::/home/git:/usr/bin/git-shell``` 修改为服务器 git-shell 的文件路径。


总结体会：

1. 熟悉 linux 对于一名工程师，我觉得真的是必要的，目前工作中是越来越接触的多 linux 命令了。能深入了解最好，最起码也要保证基础的 linux 的基本操作。
2. 网上相关教程还是相当多，相当广，基本上遇到的问题都不是第一个，也不会最后一个，认真搜搜找找还是能找到方法。
3. 当然有些教程，因为环境，版本等问题不能直接拿来主义，但是理解本质性的问题，能更直接找到问题的关键。
4. 网上了解到，还有自动化部署相关的操作，后续需要使用到再继续写下去。












