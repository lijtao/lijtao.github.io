---
layout: post
title: git 使用小结
categories: cs
tags: [git,note]
---

假设需要开发某个功能，或者 fix 某个 bug，详细如下： 
  
### 1.更新代码
在做所有开发工作之前，先把本地代码和 git repos 同步最新：
  
	git pull

<!-- more -->  

### 2. 修改代码
本地 coding, coding 过程中，可能会有多次本地 commit，比如说， coding balabalabala，然后：

	git add . 
	git commit 
  
再 coding，然后 

	git add . 
	git commit 
  
### 3.提交代码
假设 coding 完成，需要把本地代码推送到远端 git repos，在推送之前，一定做如下动作： 

	git pull --rebase 
  
在这一步，一定不要简单的执行 `git pull`，`git pull` 相当于两个动作，先 `git featch`, 然后 `git merge`；如果本地有冲突，这样会造成本地新创建一个分支出来。如果接着把这样没有意义的分支推送到远端 git repos，会导致整个 git tree 很难看。 
  
在你 coding 期间，别人可能提交代码，提交的代码可能和你 coding 的内容冲突，如果有冲突，会给你提示，类似前面说得， 

	<<<<<<< HEAD  
	......
	=======
	...... 
	>>>>>>> branch 'master' of ......
  
有冲突没关系，找到相应冲突的地方，按照提示修改就好了。改完以后做如下动作， 

	git add . 
	git commit 
  
然后根据提示做 

	git rebase --continue 
  
完成冲突解决以后就可以把本地 coding 内容 push 到远端 git repos 了 

	git push

### 创建新仓库

	git init

### 检出仓库

	git clone /path/to/repository # 本地仓库 
	git clone username@host:/path/to/repository #远程仓库

### 推送改动

如果你还没有克隆现有仓库，并欲将你的仓库连接到某个远程服务器，你可以使用如下命令添加：

	git remote add origin <server>
	git push origin master

### git 同步父流更新

    git remote add upstream https://github.com/被fork的仓库.git
    git fetch upstream
    git merge upstream/master

### 标签

	git tag 1.0.0 1b2e1d63ff

1b2e1d63ff 是你想要标记的提交 ID 的前 10 位字符。使用如下命令获取提交 ID：
git log。你也可以用该提交 ID 的少一些的前几位，只要它是唯一的。

### 打包导出

    git archive --format zip -o site-$(git log --pretty=format:"%h" -1).zip HEAD
    git archive v0.1 | gzip > site.tgz

### 还原修改

假如你做错事，可以使用如下命令替换掉本地改动：

	git checkout -- <filename>

此命令会使用 HEAD 中的最新内容替换掉你的工作目录中的文件。已添加到缓存区的改动，以及新文件，都不受影响。

假如你想要丢弃你所有的本地改动与提交，可以到服务器上获取最新的版本并将你本地主分支指向到它：

	git fetch origin
	git reset --hard origin/master

### 强制推送本地库到服务器

     git push origin HEAD --force

### 有用的贴士

内建的图形化 git：

	gitk

彩色的 git 输出：

	git config color.ui true

显示历史记录时，只显示一行注释信息：

	git config format.pretty oneline

交互地添加文件至缓存区：

	git add -i
