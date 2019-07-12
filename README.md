# homework-linux

在linux上部署⼀一个 git 服务参考:https://juejin.im/post/5a7802846fb9a063317c2e92

###第一步
1. 安装git`yum install -y git`
2. 通过`useradd chenran && passwd chenran`添加用户chenran并设置密码
3. 在服务器上选择某一个目录作为版本库存放地址。
```
// 创建仓库目录
mkdir -p test/chenran
// 创建新建的项目地址
mkdir -p aa.git
// 项目初始化（不使用--bare则会生成.git目录，版本历史记录文件存放在目录里）
git init --bare aa.git
```
以上三步通常在github/gitlab类似网站上来初始化，现在只是换到了自己的远程服务器上。
这个时候其实已经完成了git服务的部署。

###第二步
1. 配置ssh秘钥，如果没有需要新生成一个。[git生成秘钥官方指引](https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
`$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"` // 换成相应的email
2. 本地配置好秘钥后把本地的authorized_keys导入到服务器

###第三步
远程服务器改用RSA方式登录
