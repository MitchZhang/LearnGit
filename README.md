<<<<<<< Updated upstream
![](http://i.imgur.com/3KC3GGK.png)# LearnGit #
## 1.Git的安装 ##
=======
# LearnGit #
## 1. Git的安装 ##
>>>>>>> Stashed changes
- Windows 用户可以可以去[官网](https://git-for-windows.github.io/)下载git bash.exe。

- Ubantu用户需要打开终端依次执行以下命令:

 + sudo add-apt-repository ppa:git-core/ppa
 + sudo apt-get update
 + sudo apt-get install git

> 最后一顿确认之后，输入git version 得到版本为2.11表示安装成功。

## 2. Git本地设置 ##
### 2.1 配置个人信息 ###
- git config --global user.email "youremail@droi.com"
- git config --global user.name "yourname"
> 设置完成后，使用git config --list 核实一下。

### 2.2 生成SSH秘钥 ###
-  ssh-keygen -t rsa -b 4096 -C "youremail@droi.com"
> 执行完成之后，进入~/.ssh目录，如果有id_rsa.pub则表示生成成功。

### 2.3 配置秘钥###
- 首先打开Gerrit[官网](http://10.20.40.19:8080/)
- 使用你的姓名全拼加上Windows开机密码进行登陆。
- 设置Gerrit邮箱。

 + ![](http://i.imgur.com/3lQ8QyV.png)
 + ![](http://i.imgur.com/h9t6b4p.png)
 + 填入公司邮箱，点击确定，会收到一封验证邮件。

- 设置SSH秘钥

 + ![](http://i.imgur.com/EnCNE4i.png)
 + 点击addkey 将上一步生成的ssh key复制粘贴进去。
 
> 设置ssh之后你的提交才会被远程接受。

# 3. Git基本概念 #
## 3.1 工作区 ##
git init之后我们能够看到的目录。

> ls -ah 可以看到隐藏的.git目录。

## 3.2 暂存区 ##
工作区中的.git目录里面的index就是暂存区。我们git add file文件的修改就会放到暂存区，git commit之后才会存入到版本库。

## 3.3 版本库 ##
隐藏的.git目录就是我们的git版本库，里面存储了很多东西，比较重要的就是index(暂存区)，和一个指向当前分支的指针HEAD。

## 3.4 HEAD指针 ##
项目初始化之后，HEAD就指向.git为我门生成的master分支上，每一次提交HEAD指针就会往前走一次，指向最新生成的提交的地方。如下图所示：

 + ![](http://i.imgur.com/UI6YSkk.png)

## 3.5 分支 ##
当我们创建一条分支，该分支的代码就和主分支master代码一模一样。Git的分支非常强大，创建，删除，合并分支的操作都可以在1s内完成，在速度上面svn是不能比的。我们平时修复bug,开发新功能等都是在分支上操作的，完成了需求之后，我们会将分支进行合并，然后删除新建的临时分支。

> 平时开发我们应该遵守以下规则：远程上面一版会有二条分支，一条是master分支，另外一条是dev分支。master分支需要保持稳定，平时不会在这条分支上面干活，dev分支是我们平时干活的分支。每当有发版任务的时候，我们才会将dev分支上面的内容合并到master分支上。对于gerrit来说，我们平时的提交都会提交到review分支，然后由部门负责人合并到master分支。

# 4.Git基本命令 #
## 4.1 git init ##
初始化git仓库，使用git管理该项目。

## 4.2 git status ##
 查看工作区文件的状态，已经提交到暂存区会绿色显示，反之红色显示，如果工作区没有任何修改，提示工作区是干净的。

## 4.3 git diff ##
显示出各个文件的改动。

## 4.4 git reset ##
+ 版本回退命令,使用git reset + commitid 可以回退到该id下的状态。
这里有一个比较重要的 参数 --hard 如果带上了--hard,那么会回退到该id下的状态并且工作区修改也会被丢弃。此时使用git status 会提示你工作区是干净的. 如果没有使用--hard,那么你上一个commit id 的工作区的修改不会被丢弃，还会存在，这里需要看情况使用。
如果你不想去查询commit id, 你可以使用 git reset HEAD^  将git 指针往后移动一个，如果想移动二个那么加二个^即可，以此类推。往后移动50个则为git reset HEAD~50.
+ 还有一个比较重要的用法，丢弃暂存区的修改(git reset HEAD + filename)

## 4.5 git log ##
查看当前分支下的提交记录。如果带上--graph 可以显示分支合并图。
![](http://i.imgur.com/vMI9lFj.png)

## 4.6 git reflog ##
这个命令会显示你所有的git操作。适用于以下场景：你刚刚回退了一个版本，发现回退错误了，现在又想回退回去，那么git log 是不会显示已经被回退过的版本的commit id，这个时候你需要使用reflog 打开操作记录然后找到之前的commit id，然后再使用git reset + id回退过去。
![](http://i.imgur.com/2s6K0c2.png)

## 4.7 git checkout -- file ##
- 撤销修改，如果你改了工作区的一个文件，但是你又不满意，那么你可以git check out 该文件，丢弃工作区的修改。如果已经添加到了暂存区，那么你需要先使用git reset HEAD + filename,将该filename文件从暂存区撤销，再checkout -- file 丢弃工作区的修改.
- git checkout 可以用于切换分支,git checkout name ,就是切换到name分支。git checkout -b name,创建name分支，并且自动切换到name分支。

## 4.8 git clone ##
克隆远程库到本地。

## 4.9 git branch ##
查看当前分支。

-  git brach name 创建一个名为name的分支。
-  git branch -d name 删除名为name的分支。

## 4.10 git stash ##
保存当前工作区的内容,使用后当前工作区的修改全部清空，但会保存起来，适用于开发到一半突然来了一个需要解决的bug,但是你现在工作区的内容只完成了50%还无法提交，那么就可以使用这条命令暂存，然后去解决bug,完成之后再从stash恢复。

- git stash list 查看当前所保存的stash 信息,如下图所示。

 ![](http://i.imgur.com/Qf8yMh2.png)

- git stash pop stash@{0} 将工作区的内容恢复到stash@{0}的状态，并且删除stash list中对应保存的记录。
- stash apply stash@{0} 将工作区的内容恢复到stash@{0}的状态。
-  git stash drop stash@{0} 删除stash list中的stash@{0}




