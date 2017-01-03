# LearnGit #
## 1.Git的安装 ##
- Windows 用户可以可以去[官网](https://git-for-windows.github.io/)下载git bash.exe。

- Ubantu用户需要打开终端依次执行以下命令:

 + sudo add-apt-repository ppa:git-core/ppa
 + sudo apt-get update
 + sudo apt-get install git

> 最后一顿确认之后，输入git version 得到版本为2.11表示安装成功。

## 2.Git本地设置 ##
### 2.1配置个人信息 ###
- git config --global user.email "youremail@droi.com"
- git config --global user.name "yourname"
> 设置完成后，使用git config --list 核实一下。

### 2.2生成SSH秘钥 ###
-  ssh-keygen -t rsa -b 4096 -C "youremail@droi.com"
> 执行完成之后，进入~/.ssh目录，如果有id_rsa.pub则表示生成成功。

### 2.3配置秘钥###
- 首先打开Gerrit[官网](http://10.20.40.19:8080/)
- 使用你的姓名全拼加上Windows开机密码进行登陆。
- 设置Gerrit邮箱。

 + ![](http://i.imgur.com/3lQ8QyV.png)
 + ![](http://i.imgur.com/h9t6b4p.png)
 + 填入公司邮箱，点击确定，会收到一封验证邮件。

- 设置SSH秘钥

 + ![](http://i.imgur.com/EnCNE4i.png)
 + 点击addkey 将上一步生成的ssh key复制粘贴进去。
 
> 设置ssh之后你的提交才会被远程接受，



