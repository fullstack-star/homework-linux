# homework-linux

## Linux上部署git服务器的心得

1. Linux安装软件使用yum包管理器比较方便，一键安装，不需要配置环境变量，比如安装`git`:
```
yum install git
```
```
执行命令之后，可以查看一下版本，可能安装的版本不是最新，是较为稳定的版本

git --version
```

2. 创建git仓库之前，Linux需要新建git用户，专门管理git仓库。Linux系统注重用户权限隔离，一个用户/用户组管理一个应用。

3. 登录git服务器使用SRA方式更为安全，本地使用`ssh`生成密钥：
```
ssh-keygen -t rsa -C "test.name@email"
```
执行命令（回传即可）后生成：`id_rsa` 私钥、`id_rsa.pub` 公钥，把公钥配置到服务器的git用户下，这样就能确保用户登录的唯一性。