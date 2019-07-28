# task
部署一个git服务

## 实现思路
1. 首先部署一个git仓库，使用http（密码）连接  
    1. 创建git专用user，用户权限隔离
    1. 创建git家目录下的相关文件夹
    1. 安装git应用，初始化仓库
    1. 修改归属、权限以及指定git用户的shell类型
1. 然后实现rsa连接（和github一样，2种方案并存）  
    1. 在～/.ssh文件夹，添加一对rsa
    1. 将公钥上传到服务器的/home/git/.ssh/authorized_keys
1. 最后完善服务端git仓库  
    1. ip指向改为域名指向，更加友好
    1. git用户改为用户组，实现协作


## 具体步骤
### 1.部署一个git仓库，实现http连接
```js
/* 1.创建用户 */
root@remote /> useradd -m git // -m参数会在/home下建立同名文件夹，效果类似adduser
root@remote /> passwd git // 一定要写上用户名，否则默认更改的是当前用户

/* 2.创建git相关文件 */
root@remote /> rmdir -p /home/git/repos/a.git // -p会默默帮你补上中间文件夹，而不是报错
root@remote /> yum install git // 安装git应用
root@remote /> git init --bare /home/git/repos/a.git // 初始化空仓库

/* 3.修改归属和文件权限 */
// 修改文件（夹）归属
root@remote /> chown -R git:git /home/git/repos
// 修改文件夹权限,权限最小化
root@remote /> chmod chmod 770 /home/git /home/git/repos
root@remote /> chmod -R chmod 770 /home/git/repos/a.git

/* 4.更改用户配置 */
root@remote /> vim /etc/passwd
// 修改git用户的shell类型为git-shell

/* 5.客户端测试 */
admin@local anydir> git clone git@<ip>:/home/git/repos/a.git // 提示一个空的仓库已经下载完成
// (修改一点内容...此处略）
admin@local anydir> git add . && git commit -m "init" && git push origin master // 提示成功
// (跑去服务端的a.git可以看到 a.git/object文件夹有变动)
// 本地删除a.git,再克隆一次看看
admin@local anydir> rm -rf a.gif
admin@local anydir> git clone git@<ip>:/home/git/repos/a.git
// 查看确实是修改之后的内容，至此git服务部署成功了

```


### 2.实现rsa连接
要实现rsa连接，本地当前用户的.ssh目录下必然存在一个公钥，与远端需建立连接的用户.ssh目录下的公钥，完全一致即可。

```js
// 本地用户创建一对rsa（可以使用现成的，非必须）
admin@local .ssh> ssh-keygen -t rsa -C "your@newEmail" // 问题：每个应用对应一个email，哪来那么多email啊？
// 将新的公钥拷贝到远端指定目录下
admin@local .ssh> scp <你的本地公钥文件> git@<ip>:/home/git/.ssh/authorized_keys
/* 此时，我们可以建立无密码的连接了 */
admin@local anydir> git clone git@<ip>:/home/git/repos/a.git
```


### 3.完善服务端git仓库  
到目前为止还不能用于实际场景，原因是：
1. ip指向改为域名指向，更加友好
1. git用户改为用户组，实现协作
```js
/* 专有域名 */
// 增加dns解析，记录类型为A，主机记录（二级域名）为git，记录值指向服务器公网ip
// 修改当前用户的 .ssh/config文件，编辑如下：
Host yourdomain.com
	HostName git.yourdomain.com
	user git
    port 22
	IdentityFile <你的公钥文件地址>
// 此时，你可以这样来git操作了,简单了很多
admin@local anydir> git clone yourdomain:/home/git/repos/a.git
```
```js
/* 多人协作 */
// 如果有多人协作，公用一个git账号就太low了
// 此时按需添加开发者账号，限制不能登录，自行修改密码。
// 然后把这些账号都加入到git组
root@remote /> usermod -a -G git <某开发者>
// done！
```

## 思考
*仓库和用户组之间的关系

一个仓库对应一个或多个开发组，对开发组设置权限。
比如核心开发组、外包开发组什么的。
但增删项目和增删用户挺麻烦需要一次次改。
怎么实现自动化操作？

*像github的pull request功能，实现思路是什么？
