# homework-linux
> 安装前思考，git如何做用户隔离，装在哪个目录合适

## 操作思路以及遇到的问题

1. 使用yum安装
2. 检查是否存在git用户，发现没有git用户，添加git用户、设置git密码
3. 服务器创建git仓库，本地进行clone，输入密码，
4. 发现密码错误，重置用户密码


```
passwd git
```
​
5. 发现密码需要设置超过8位，并且不能太过简单
6. 发现ecs网页命令终端巨卡，尝试本地终端ssh登录，使用ssh root@公网ip尝试登录成功。
7. 本地使用密码clone远程仓库成功
8. 尝试使用密钥clone

## 实现步骤记录
> 网络很多文章跑不通，希望下面步骤操作可以帮助想尝试的同学

### ecs 实现ssh免密登录
服务器


​
```
cd ~/.ssh/

ls

authorized_keys  id_rsa  id_rsa.pub  known_hosts

vim authorized_keys
​
```

打开编辑界面，放入本地客户端公钥

保存退出

使用ssh root@服务器ip回车即可登录



本地

```
​
cd ~/.ssh/

cat id_rsa.pub
​
```

获得公钥内容，放入上面打开的编辑界面中，保存

### git ssh克隆实现
本地客户端

```
// 本地客户端生成密钥
ssh-keygen -t rsa -C "user@qq.com"  

// 上传本地客户端公钥到git用户服务器上
scp -r ~/.ssh/id_rsa.pub git@服务器ip:~/
```
​
远程服务器

```
  cd ~

  ls

  id_rsa.pub

  cd .ssh

  cat ~/id_rsa.pub >> ~/.ssh/authorized_keys

  chmod 600 ~/.ssh/authorized_keys

  chmod 700 ~/.ssh

  su //切换root用户

  vim /etc/ssh/sshd_config // 修改下面三项的值，没有的话，加入该配置文件

  RSAAuthentication yes
  PubkeyAuthentication yes
  AuthorizedKeysFile      .ssh/authorized_keys

  service sshd restart //重启配置
```


本地客户端 终于实现了ssh克隆远程服务器仓库

```


  git clone git@服务器ip:/home/gitrepo/runoob.git

  Cloning into 'runoob'...

  warning: You appear to have cloned an empty repository.
```