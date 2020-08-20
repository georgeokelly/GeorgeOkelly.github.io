---
title: Git
tags: 
  - "git"
categories: Computer
keywords: Git
description: 本文是关于Git的笔记。
cover: 
date: 2020-08-20 17:03:22
katex: true
---



# Git

分布式版本控制系统，区别于SVN等集中式版本控制系统。

## 版本库、工作区、暂存区

### 版本库(repository)

repo中所有文件都可以被git管理，修改、删除都可以被git跟踪、还原。Git跟踪的是<u>**修改**</u>而不是<u>**文件**</u>。

### 创建repo

```shell
$ mkdir <dir name>
$ cd <dir name>
$ git init
Innitialized empty Git repository in /path/to/repo
```

可建立一个空repo，当前目录多了`.git` 的目录，是Git用来跟踪管理版本库的，可用`ls -ah` 来查看。

### 把文件添加至repo

版本控制系统只能跟踪**文本文件**的改动（如txt，网页，代码文件等），图片、视频（还有word）这类二进制文件，只能知道大小变化。建议使用标准等UTF-8编码。

```shell
$ git add <file>
$ git commit -m <message>
```

`git add`将文件添加至repo，而`git commit`可以作一个提交。`-m`后面输入的是本次提交的说明，比如`git commit -m "add 3 files."`。`git add`可以反复多次使用添加多个文件，实际上就是把文件修改添加到暂存区，然后用`git commit`完成提交到当前分支。

### .git目录

Working Directory有一个隐藏目录`.git`，里面即是Git的版本库。其中最重要的就是Stage（暂存区），以及Git自动创建的第一个分支`master`和指向`master`的指针`HEAD`。

### 工作区(working directory)&暂存区(stage/index)

工作区是能直接查看到文件的区域。

暂存区就是缓存文件修改的区域，位于repo中。

## 时光机穿梭和版本回退

`git status` 命令可以查看工作区的状态：是否被修改过（但还没有提交）。

`git diff` 命令查看被修改的内容/difference，显示的格式是Unix通用的diff格式。`git diff HEAD -- <file>` 可以查看工作区和版本库里最新版本的区别。

`git log` 命令查看版本控制系统中，版本修改的历史记录。加上`--pretty=oneline`参数可以简化显示。

> 注：`commit id`(版本号)是SHA1计算出的十六进制数字，不同于SVN递增的版本号，Git用大串数字满足分布式的需求。

`git reflog` 命令用来记录Git的每一次提交（的commit id）。

### git reset

`git reset --hard <commit id>`即可回退到版本号对应的版本。其中commit id可以只打前面若干个数字（如果能唯一确定某个版本的话）。还可以用`HEAD`来回溯最近的版本。

`git reset HEAD <file>` 可以把暂存区的修改撤销掉（unstage），将其修改重新放回工作区。

```shell
HEAD         #当前版本
HEAD^        #上一个版本
HEAD^^       #上上个版本
HEAD~100     #上100个版本
```

### git check

`git check -- <file>` 命令可以把工作区的修改(包括删除)全部撤销。包含两种情况：

1. 文件自修改后还没有放到暂存区，则撤销后和版本库一模一样；

2. 文件已经添加到暂存区，又作了修改，则撤销后和添加到暂存区时一样。

总之是让文件回到最近一次`git add` 或者 `git commit` 的状态。

`git rm <file>` 命令可以删除（或者说是修改）版本库的文件，然后执行`git commit` 完成删除。（先删文件后，`git rm <file>`和`git add <file>`是一样的）

## 分支管理

### 分支管理示意

1. 一开始仅有master一条分支，指针`master`指向最新的提交，而`HEAD`指向`master`；

<img src="https://octopus.smcdn.cn/basic/_ca635dabe4f831ef5d543f53e7a0c382/md5/85c0a7692b7d4020fe840c1dfea09acf.png" title="" alt="" data-align="center">

2. 当创建一个新分支`dev`时，新指针`dev`被创建并指向`master`相同的提交，而`HEAD`指向`dev`表示当前分支在`dev`上；

<img title="" src="https://octopus.smcdn.cn/basic/_ca635dabe4f831ef5d543f53e7a0c382/md5/997d708c4219d4623e1d5cce844e94ce.png" alt="" data-align="center" width="303">

3. 之后工作区的提交则是`dev`分支；

<img src="https://octopus.smcdn.cn/basic/_ca635dabe4f831ef5d543f53e7a0c382/md5/2fb188615e54d08315b3d27ee95e7963.png" title="" alt="" data-align="center">

4. 如果`dev`分支上的工作完成了，想合并`dev`和`master`分支，只需要把`master`指针指向`dev`分支的当前提交。合并完成后删除`dev`指针，就只剩下`master`分支了。

<img src="https://www.liaoxuefeng.com/files/attachments/919022412005504/0" title="" alt="git-br-ff-merge" data-align="center">

### 分支创建、切换和删除

```shell
$ git branch <branch name>       # 创建某个branch
$ git checkout <branch name>     # 切换至某个branch
or
$ git checkout -b <branch name>  # '-b'表示创建并切换
```

`git branch` 命令可以查看当前分支。它会列出所有分支，并在当前分支前标记`*`号。

`git merge` 命令用于合并指定分支到**当前分支**。比如当前在`master`分支，且要合并`dev`分支，那么使用命令为`git merge dev`即可。

【**推荐**】最新版本的Git使用`git switch`来切换分支。

```shell
$ git switch -c <branch name>   # 创建并切换到新的分支

$ git switch <branch name>      # 直接切换到已有的分支
```

`git branch -d <branch name>` 命令可以删除某个分支。

### 冲突

当Git无法自动合并分支时，就要首先解决冲突，再提交进行合并。解决冲突当方法就是把造成合并失败的文件手动编辑为希望的内容再提交。`git log --graph`可以看到分支合并图。

通常合并分支会用`Fast forward`模式，但这种模式下删除分支后会丢掉分支信息。如果强制禁用该模式，那么merge时会生成一个新的commit，这样就可以从分支历史上看到分支信息。

```shell
$ git merge --no-ff -m <message> <branch name>
# "--no-ff"参数表示禁用Fast forward模式；注意要添加commit
```

其效果如图：

<img src="https://octopus.smcdn.cn/basic/_ca635dabe4f831ef5d543f53e7a0c382/md5/c3c51c2e01b0a17feb54eb8f3d9d2eda.png" title="" alt="" data-align="center">

### 分支策略

实际开发中应按照几个基本原则进行分支管理：

- 首先`master`分支应该是非常稳定的，仅用来发布新版本；

- 在其他分支修改、提交，到某个时候再合并到`master`分支上；

### Bug 分支

假设需要修复`dev`分支上一个代号为101的bug，会想创建一个`issue-101`分支来修复它，但此时`dev`分支上进行中的工作还没有提交。那么可以用`stash`功能，将当前工作区现场保存，等修复完成后恢复现场继续工作。如：

```shell
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```

**注意**：修复哪个分支，就在哪个分支上创建临时分支。

`git stash list` 命令查看保存了的现场。

`git stash apply` 命令恢复现场，但是stash内容并不删除，需要用`git stash drop`来删除。也可以用`git stash pop` 恢复现场的同时也把stash内容删了。可以多次stash，恢复的时候先用`git stash list`查看，然后恢复到指定的stash，用`git stash apply stash@{<number>}`。

对于多个有同个bug的分支，可以用`cherry-pick`命令，**<u>复制特定的一个提交</u>**（这个例子里是`issue-101`）到当前分支，比如：

```shell
$ git branch
* dev
  master
$ git cherry-pick 4c805e2
[master 1d4b803] fix bug 101
 1 file changed, 1 insertion(+), 1 deletion(-)
```

注意到这里两个分支下commit id不相同，因为尽管它们改动相同，但确实是两个不同的commit。

### Feature 分支

假设工程需要添加一个新feature，最好新建一个feature分支，在上面开发。

如果没有合并就需要去掉该feature，使用命令删除：

```shell
$ git branch -d <branch name> # 删除分支
or
$ git branch -D <branch name> # 强制删除分支
```

## 多人协作

### 查看远程库的信息

```shell
$ git remote
origin
$ git remote -v
origin git@github.com:XXX.git (fetch)
origin git@github.com:XXX.git (push)
```

### 推送分支

推送分支就是把该分支上的所有提交推送到远程库，推送时要指定本地分支，如：

```shell
$ git push origin master
$ git push origin dev
```

但是并非所有本地分支都需要远程推送：

- `master`分支是主分支，因此要时刻与远程同步；

- `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要远程同步；

- `bug`分支只用于本地修复bug，就没必要推送远程；

- `feature`分支是否推送取决于是否团队合作开发feature。

### 抓取分支

多人协作时团队各成员会在`master`和`dev`分支上推送各自的修改。但clone默认情况下只能看到本地的`master`分支。如果要在`dev`分支上开发，就必须创建远程origin的`dev`分支到本地。

```shell
$ git checkout -b dev origin/dev
# git checkout -b <branch name> origin/<branch name>
```

异步修改后直接`git push`很可能会失败，这时先用`git pull`把最新的提交从`origin/dev`上抓下来，然后在本地合并解决冲突，再做推送。如果`git pull`也失败了，可能是因为没指定本地`dev`分支与远程`origin/dev`分支的链接：

```shell
$ git branch --set-upstream-to=origin/dev dev
Branch 'dev' set up to track remote branch 'dev' from 'origin'
# git branch --set-upstream-to=origin/<branch name> <branch name>
```

之后`git pull`，确保没有冲突后合并后再`git push`。

### rebase

rebase操作(`git rebase`)可以通过修改本地分叉提交的方式将分叉整合成一条直线，看上去更直观。

## 标签管理

tag可以和commit绑在一起，相比commit id，tag更容易被记住。创建的标签都只存储在本地，不会自动推动到远程库。

### 创建标签

```shell
$ git branch
* dev
  master
$ git checkout master
Switched to branch 'master'
$ git tag v1.0 # git tag <tag name> 默认标签是打在最新的commit上的
$ git tag      # 查看所有标签
v1.0

or
$ git tag <tag name> <commit id>
```

标签不是按时间顺序列出的，而是按字母排序的，可用`git show <tagname>`查看标签信息。

```shell
$ git tag -a <tag name> -m <message> <commit id>
# '-a'指定标签名，'-m'指定说明文字，可用git show <tag name>查看说明文字
```

### 操作标签

`git tag -d <tag name>` 命令删除标签。

`git push origin <tag name>` 命令推送某个标签到远程库。

`git push origin --tags` 命令一次性推送全部尚未推送到远程库的本地标签。

`git push origin :refs/tags/<tag name>` 命令删除远程库中的标签。

## GitHub

(optional) `ssh-ketgen -t rsa -C "email@example.com"` 生成密钥对。在GitHub上 ADD SSH Key，将`id_rsa.pub`的内容复制到Key文本框内。

### 关联远程仓库

```shell
$ git remote add origin git@github.com:XXX.git
```

`origin` 是Git远程库的默认叫法，也可以改成别的（不推荐）。

将本地 master 分支的最新修改推送至GitHub，使用命令：

```shell
$ git push origin masteror
$ git push -u origin master # 对于空的远程库，第一次推送master分支，'-u'参数会关联且推送
```

`git clone` 命令支持ssh 和 https等多种协议；

- 在GitHub上，可以任意fork开源仓库；

- 自己拥有fork后的仓库的读写权限；

- 可以推送pull request给官方仓库贡献代码。

## 例子：Hexo

### Install tools for Hexo

1. [git](https://git-scm.com/downloads)
2. [nodejs](https://nodejs.org/en/)

可以通过以下命令查看是否安装成功；

```shell
git --version

npm -v
node -v
```

### Create a Github repo

项目必须要遵守格式：账户名.github.io，不然接下来会有很多麻烦。并且需要勾选Initialize this repository with a README.

访问 name.github.io 可以检查是否创建成功；

### Install Hexo

执行 `npm install hexo -g` 安装Hexo。用 `hexo -v` 查看是否成功；再用 `hexo init` 初始化该文件夹。（看到“Start blogging with Hexo!”一般就成功了，不放心可以注意下打印信息）

输入`npm install` 安装所需组件。

`hexo g` 生成，可以用 `hexo s` 开启本地服务器，访问对应网址就可以检查blog的状态了。（如果端口被占用可用 `hexo server -p [your port]` 来改变端口号）

### Configure Github repo

```shell
#配置github账号和邮箱信息
git config --global user.name "your name"
git config --global user.email "your email"

#检查.ssh文件夹是否存在
ls ~/.ssh
#config  known_hosts

#生成设备的密钥并添加至ssh-agent
ssh-keygen -t rsa -C "your email"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

之后在GitHub账户上的SSH and GPG keys中添加ssh key，将id_rsa.pub文件中的内容复制上去。

输入 `ssh -T git@github.com` 测试添加ssh是否成功。

最后配置blog文件夹里的 `_config.yml` 文件末尾处。

```yaml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: git@github.com:name/name.github.io.git
  branch: master
```

### Test 

新建一篇博文 `hexo new post "blah blah"`

安装一个扩展 `npm install hexo-deployer-git --save`

生成并部署到主页 `hexo d -g` ，访问https://name.github.io查看。

## 参考资料

1. [Git教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600)

2. [Git Cheat Sheet](https://gitee.com/liaoxuefeng/learn-java/raw/master/teach/git-cheatsheet.pdf)

3. [Git 官网]([http://git-scm.com](http://git-scm.com/)) (http://git-scm.com)
