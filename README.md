# LearnGit #
## 1. Git的安装 ##
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
-  ssh-keygen -t rsa -b 4096 -C "youremail"
> 执行完成之后，进入~/.ssh目录，如果有id_rsa.pub则表示生成成功。

### 2.3 配置秘钥###
- 首先打开Gerrit官网,公司内网。
- 使用你的姓名全拼加上Windows开机密码进行登陆。
- 设置Gerrit邮箱。
	

![](http://i.imgur.com/pGthh2t.png)
![](http://i.imgur.com/UfBXhqC.png)

 + 填入公司邮箱，点击确定，会收到一封验证邮件。

- 设置SSH秘钥

 ![](http://i.imgur.com/Fo2IbYt.png)
  点击addkey 将上一步生成的ssh key复制粘贴进去。
 
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

  ![](http://i.imgur.com/UI6YSkk.png)

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

## 4.11 git remote -v  ##
显示远程版本库的信息.

![](http://i.imgur.com/HFxAdFh.png)
> 如果是一个已有的项目和远程版本库建立联系需要使用命令:git remote add origin url。

## 4.12 git rm --file ##
 删除版本库上的文件。

## 4.13 git merge branchName ##
将名为branchName的分支合并到当前分支。合并之后如果有冲突，需要解决冲突之后才能推送到远程。
合并分支的时候需要 带上 --no-ff 参数，这样合并之后会生成一条commit信息。完整命令（git merge --no-ff aaa -m “xxx”）,这样如果以后出了问题可以查出是那一条分支合并的时候有问题。如果不带--no-ff将不会知道分支是哪里开始合并。

## 4.14 git push origin branchName ##
向远程推送名为 branchName分支的内容。

## 4.15 git pull origin branchName ##
同步远程名为branchName分支的内容到本地。

# 5. Gerrit已知的坑 #
## 5.1向远程推送的区别 ##
在向远程推送本地仓库的修改时我们需要注意，使用**Gerrit**一定要用git push origin HEAD:refs/for/master。我们平时向github上面推送代码都是用的git push origin master，使用gerrit 会报错，说权限拒绝，因为我们无权直接将代码推送到主分支master,再gerrit中我们需要先将代码推送到CodeReview分支，审核通过后才能合并到master分支上。

## 5.2 在Gerrit新建一个远程仓库，推送本地信息到远程时出现错误确实change id ##
下面这段代码是在gerrit web控制端生成的 clone 代码.
git clone ssh://zhangmingzhe@10.20.40.19:29418/FreemeLite && scp -p -P 29418 zhangmingzhe@10.20.40.19:hooks/commit-msg FreemeLite/.git/hooks/  这样clone下来的库会在.git目录建立一个hooks 目录，这是一系列的钩子函数,里面有各种脚本，其中 commit-msg 会在你提交的时候(git commit -m “xxx”)触发钩子，生成一个change-id,这个changeid 用于gerrit web 控制端的，如果没有这个id,向远程push会报错。

![](http://i.imgur.com/isItU6U.png)

那么问题来了，当我们有一个已经完成的项目需要推送到远程的时候，git init 之后，在.git目录下是不会有 hooks/这个目录，那么提交的时候不会产生change-id，导致最终推送不到远程。解决方法就是：在一个有hooks 目录的项目中将其拷贝到没有hooks的目录中,更新没有changid 的提交，再推送到终端。

# 6.使用AndroidStudio进行Git操作 #
有的时候还是用IDE集成一些操作比较直观和方便，下面介绍如何使用AndroidStudio进行Git操作。

## 6.1 初始化git仓库 ##
![](http://i.imgur.com/osx7EO8.png)

- 点击Ceate Git Repository 之后会弹出一个路径选择让你选择一个项目用git去管理。这一步操作等同于 git init。

## 6.2 添加到暂存区 ##
选择任意一个项目初始化之后，我们看看项目的变化.

![](http://i.imgur.com/QhNVvd3.png)


文件全部是红色的。一点没错，因为在终端里面,如果没有使用git add . 命令添加代码的话，就表明文件是未被追踪的，只有提交到暂存区之后，才会变成绿色，当然AndroidStudio也会有对应得操作,如下:

![](http://i.imgur.com/sapkC3i.png)

- 可以看到点击了add之后文件都变成了绿色，保存到了暂存区。此操作等于 git add .。

## 6.3 提交到版本库 ##
![](http://i.imgur.com/UTrBfth.png)
![](http://i.imgur.com/X4kVdns.png)

- 如上图所示：这个界面我们非常熟悉了，它等同于git commit 和 git diff 的联合，我们可以点击文件查看不同的内容，不过这个比直接在终端输入更加直观，极大提高我们的速度效率

## 6.4 推送到远程  ##
代码提交之后我们需要及时推送到远程。
![](http://i.imgur.com/pE32sY7.png)

点击Push之后如下图所示:
![](http://i.imgur.com/xdQzsmP.png)

因为我们是第一次推送到远程，所以我们需要定义远程的Origin，点击define remote如下图
![](http://i.imgur.com/2CQKps6.png)

这个时候我们需要往，url 里面填入我们再远程新建的项目地址。
这一步操作相当于设置远程Origin,具体操作可以看 Git基本命令中的第11条命令；
设置完url,点击确定之后，如下图所示:

![](http://i.imgur.com/lcmzvIK.png)

AndroidStudio自动推送到远程master分支。
在这个界面点击Push之后，会验证你的身份。

![](http://i.imgur.com/5pMGz7d.png)

在这个界面填入你的Gerrit或者是Github的账号密码就可以向远程push代码了。

## 6.5 进行分支操作 ##
![](http://i.imgur.com/k4OCbjy.png)

![](http://i.imgur.com/z45komM.png)

如上图所示，点new Branch可以新建分支，等同于命令(git checkout -b dev).当前正在使用的分支是dev,如果我想切换到aaa,那么如下所示:

![](http://i.imgur.com/UT5Sf1q.png)

四个功能依次是，切换分支，合并分支，对分支的重新命名和分支的删除，这里AndroidStudio对命令进行了图形化封装。

**接下来详细说下分支合并:**

![](http://i.imgur.com/WlJWSjQ.png)

点击merge之后如下图所示：

![](http://i.imgur.com/6BwTnUM.png)

可以很清楚的看到,当前分支为master 将要合并名为dev的分支.最下面是我们的commit change.需要写清楚。Merge之后的结果如下图所示，和svn类似：

![](http://i.imgur.com/q16rruI.png)
![](http://i.imgur.com/fpf7hoj.png)

如上图所示：可以选择任意的master观察其提交记录非常方便。
> 总结：上述所有的分支操作，完成时间都是1s，左右不管你有1w个文件和1个文件，git都能做到快速完成，因为git追踪管理的是文件的修改，svn跟踪管理的是文件，所以git的分支操作速度完爆svn。

## 6.6 拉取远程代码 ##
![](http://i.imgur.com/XtAXUSO.png)
![](http://i.imgur.com/nWR8H7A.png)

点击pull之后如上图所示：remote标识你远程的代码库，branches to merge表示你想要合并的分支。选择之后，点击pull即可拉取最新代码，如果有冲突，AndroidStudio会弹出一个merge框，让你解决冲突。

## 6.7 进行版本回退 ##
![](http://i.imgur.com/hGDOUgo.png)

Reset HEAD 标识一系列回退操作，点击之后如下图:

![](http://i.imgur.com/Iu0a6Z2.png)
![](http://i.imgur.com/Hqcqvxs.png)

其中reset type表示三种版本回退的类型，这里介绍一下:

- 第一种:Mixed 意思是重置暂存区的内容，但不重置工作区的内容

**栗子**

![](http://i.imgur.com/x6ir8F5.png)

结果如上图:我新建了一个TestClass文件 并且添加到暂存区，使用git status命令确认它已经被添加到暂存区,然后使用上述type=mixed ，再使用git status 可以看到，丢弃了TestClass在暂存区的修改，TestClass变成了红色。适用于将错误的信息添加到暂存区的场景。

- 第二种:soft  仅仅改变HEAD指针，并不会对工作区和暂存区有所影响。
- 第三种:hard 重置工作区和暂存区的所有内容到HEAD下.(工作区和暂存区的内容全部会被刷新).

![](http://i.imgur.com/yVWWref.png)

> 注意：如图所示，表示master将会向后回退2个提交，并且会刷新工作区，暂存区的内容，你之前左右的改动都会丢失！这条命令风险很高，所以版本回退时需要谨慎。
	