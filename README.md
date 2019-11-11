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
+ Git管理的是修改记录。`git diff`可查看工作区与暂存区区别，`git --cached`查看暂存区和仓库的区别。
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
