# Git学习简记
学习[廖雪峰Git教程](https://www.liaoxuefeng.com/wiki/896043488029600)的简单笔记。
## 本地仓库管理
### 创建版本库
+ 创建一个空的文件夹，使用`git init`进行初始化。<br>
+ 添加文件到Git仓库：
  1. 使用`git add <file>`，可在一次提交前多次添加至暂存区。
  2. 使用`git commit -m <comment>`提交到仓库。

### 版本回退
+ 多次commit的记录可使用`git log`查看，包括commit号、作者、时间、评论等，其中`HEAD`指向当前版本。精简log可用`git log --pretty=oneline`查看。
+ 回退至某个commit后的版本，`git reset --hard <commit_id>`，回退前一个版本，`git reset --hard HEAD^`。此时`git log`中`HEAD`指向回退的版本，没有后续的版本信息。
+ 若回退有误，想返回某后续版本，通过`git reflog`查看版本号，再用`git reset --hard <commit_id>`。

### 工作区、暂存区、仓库
![三个区域间的转换](/images/state.png)
+ Git管理的是修改记录。`git diff`可查看工作区与暂存区区别，`git diff --cached`查看暂存区和仓库的区别。
+ 修改工作区文件后，`git diff`显示差异。`git add <file>`后工作区修改同步到暂存区，`git diff`无差异。
+ 未提交修改时，`git diff --cached`有差异。`git commit -m <comment>`提交了修改，`git diff --cached`无差异。

### 管理修改
+ `git commit -m <comment>`只提交暂存区中的修改。
+ 若工作区中的修改未经过`git add <file>`，这个修改不会被提交。

### 撤销修改
+ 情况1：修改了工作区文件，未添加暂存区时，`git checkout -- <file>`可撤销修改。
+ 情况2：修改工作区且加至暂存区。
  1. `git reset HEAD <file>`，撤销暂存区的修改记录
  2. 类似情况1，`git checkout -- <file>`撤销修改。
+ 情况3：修改工作区，加至暂存区且提交。采用[版本回退](#版本回退)的方法。

### 删除文件
+ 确实想删除某文件<file>
  + （**推荐**）手动删除工作区文件<file>，然后`git add <file>`至暂存区，最后`git commit -m <comment>`提交修改。
  + （*慎用*）`git rm <file>`删除工作区的文件并加至暂存区，然后`git commit -m <comment>`提交修改。
+ 误删了某个文件想恢复
  + 如果在资源管理器中手动删除或使用`rm <file>`删除，相当于仅将工作区进行了修改，可采用[撤销修改](#撤销修改)中情况1的方法撤销，即`git checkout -- <file>`。
  + 如果使用`git rm <file>`删除，未提交，此时与手动删除并`git add <file>`等价，可采用[撤销修改](#撤销修改)中情况2的方法撤销。
  + 如果已经提交，可采用[版本回退](#版本回退)的方法。

## 远程仓库
### 生成SSH密钥
1. 打开git，`ssh-keygen -t rsa -C "youremail@mail.com"`，然后一直回车，生成私钥`id_rsa`和公钥`id_rsa.pub`，即SSH所需的密钥对。
2. 登录Github，打开账户Setting里的SSH Keys。
3. 用记事本打开`id_rsa.pub`文件，复制公钥，这样Github服务器就能通过公钥判别是否为秘钥持有者发送的消息。

### 添加远程库
+ 关联远程库
  1. 在Github中创建一个repo后，可得到该repo的url。
  2. 打开已有的本地仓库（已经过`git init`的仓库），使用命令`git remote add <origin> git@<server-name>:<user-name>/<repo-name>.git`。<br>其中`<origin>`为Git远程仓库的叫法，也可以改为别的。`<server-name>`通常为`github.com`，取决于是否本次修改过hostname。
+ 推送内容至远程仓库。
  + 第一次推送时使用`git push -u <origin> <master>`推送当前本地的`<master>`分支至远程的`<origin>`分支，并将`<origin>`指定为默认主机。
  + 此后则不需要添加参数，可直接使用`git push <origin> <master>`
### 克隆远程库
+ `git clone <origin> git@<server-name>:<user-name>/<repo-name>.git`
+ 上面是SSH方式，比较快，也可使用https的方式。

## 分支管理
### 创建分支和管理
+ 分支是提交的时间线，不创建新的分支时只有一条时间线`master`，`HEAD`指向`master`。<br>![只有一条分支](/images/branch1.png)<br>创建新的分支`dev`即创建了一个新的提交时间线,切换到新分支`dev`后`HEAD`指向`dev`。<br>![创建新分支](/images/branch2.png)<br>在新的分支中提交时只延长`dev`，不影响`master`分支。<br>![在新分支中提交](/images/branch3.png)<br>在新分支开发完成后，合并分支时直接将`master`指向`dev`。<br>![合并分支](/images/branch4.png)<br>最后删除分支，只剩下`master`分支。<br>![删除新分支](/images/branch5.png)<br>
+ 分支相关命令：
  + 查看分支：`git branch`
  + 创建分支：`git branch <branch-name>`
  + 切换分支：`git checkout <branch-name>`或者`git switch <branch-name>`
  + 创建+切换分支：`git checkout -b <branch-name>`或者`git switch -c <branch-name>`
  + 合并`<brach-name>`分支到当前分支：`git merge <branch-name>`
  + 删除分支：`git branch -d <branch-name>`

### 解决冲突
+ 两个分支提交了相同位置的不同修改时出现冲突，`git merge <branch-name>`后提示冲突，并标在文件中。手动修改有冲突的地方，再提交合并完成。
+ `git log --graph`查看分支合并图。

### 分支管理
+ 合并分支时使用`Fast forward`模式，删除分支后会丢失分支信息。可以加入`--no-ff`参数，这样主分支新建一个commit来合并，需要`-m`参数写入描述。
+ 开发时，`master`分支稳定，用于发布新版本，`dev`分支用于开发，是不稳定的，每个开发人又有自己的分支，向`dev`进行合并。

### BUG修复
+ `dev`分支开发时工作区进行了修改但还没有提交，突然要先修复`master`一个BUG。如果直接切换到`master`建立`issue`分支修复，开发`dev`分支时的修改仍会在工作区中，则提交时会把`dev`工作区未完成的修改与BUG修复部分一同提交。
+ `dev`分支未完成修改时，先`git stash`，可保存工作现场并清空修改，完成BUG修改后回到`dev`分支再`git stash pop`恢复工作现场。
+ 将`master`修复的BUG应用到`dev`中，需要先`git cherry-pick <commit-id>`，再`git stash pop`。也就是先通过commit或stash清空工作区修改才能cherry-pick。

### Feature分支
+ 开发新特性最好由`dev`新建一个分支。
+ 如果分支**已经commit**但是又不需要合并到`dev`了，此时在`dev`分支中按[创建分支和管理](#创建分支和管理)中删除分支的方法无法删除，应使用`git branch -D <branch-name>`强制删除。

### 多人协作
+ 工作模式：
  1. 首先，可以试图用`git push <origin> <branch-name>`推送自己的修改；
  2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
  3. 如果合并有冲突，则解决冲突，并在本地提交；
  4. 没有冲突或者解决掉冲突后，再用`git push <origin> <branch-name>`推送就能成功！
+ 相关命令：
  + 查看远程库信息，使用`git remote -v`；
  + 本地新建的分支如果不推送到远程，对其他人就是不可见的；
  + 从本地推送分支，使用`git push origin <branch-name>`，如果推送失败，先用`git pull`抓取远程的新提交；
  + 在本地创建和远程分支对应的分支，使用`git checkout -b <branch-name> <origin>/<branch-name>`，本地和远程分支的名称最好一致；
  + 建立本地分支和远程分支的关联，使用`git branch --set-upstream <branch-name> <origin>/<branch-name>`；
  + 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

### 变基
+ 变基`git rebase`
+ 参考[Git rebase文档](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)和[Git rebase详解](https://www.cnblogs.com/pinefantasy/articles/6287147.html)