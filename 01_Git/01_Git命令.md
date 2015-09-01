<a name="catalog"/>
## 目录
* [git push](#git_push)
* [git remote](#git_remote)
* [git stash](#git_stash)
* [git fetch](#git_fetch)
* [git rebase](#git_rebase)

<a name="git_push"/>
### git push ([返回目录](#catalog))
`git push`命令用于将本地分支的更新，推送到远程主机，它的格式与`git pull`命令相仿，格式如下：

	$ git push <远程主机名> <本地分支名>:<远程分支名>

**注意：**
>分支推送顺序的写法是<来源地>:<目的地>，所以git pull是<远程分支>:<本地分支>，而git push是<本地分支>:<远程分支>。

	$ git push osc master:master
	$ git push github master:master

**说明：**
>以上命令表示，把本地 master 分支的更新推送至 osc 和 github 主机的 master 分支上。

如果省略远程分支名，则表示将本地分支推送至与之存在”追踪关系”的远程分支(通常两者同名)，如果该远程分支不存在，则会被新建。

	$ git push osc master

**说明：**
>以上命令表示，把本地 master 分支的更新推送至 osc 主机的同名(即 master)分支上。<br/>如果后者不存在，则会被新建。

如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支。

	$ git push osc :master
	# 等同于一下命令
	$ git push osc --delete master

**说明：**
>以上命令表示，删除 osc 主机上的 master 分支。

如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。

	$ git push osc

**说明：**
>以上命令表示，将当前分支推送到 osc 主机的对应分支。

如果当前分支与多个主机存在追踪关系，则可以使用`-u`选项指定一个默认主机，这样后面就可以不加任何参数使用 git push。

	$ git push -u osc master

**说明：**
>以上命令说明，将 本地的 master 分支推送至 osc 主机的 master 分支上；并指定 osc 主机为默认主机，在后续的操作中，如果没有指定主机名，则默认操作 osc 主机。

不带特殊参数的 git push，默认只推送当前分支，这叫做`simple`方式；此外，还有一种`matching`方式，会推送所有`有对应的远程分支`的`本地分支`。Git 2.0版本之前，默认采用 matching 方法，现在改为默认采用 simple 方式。如果要修改这个设置，可以采用 git config 命令。

	$ git config --global push.default matching
	# 或者
	$ git config --global push.default simple

还有一种情况，就是`不管是否存在对应的远程分支`，将本地的所有分支都推送到远程主机，这时需要使用–all选项。

	$ git push --all osc

**说明：**
>以上命令表示，将所有本地分支都推送至远程主机，无论该本地分支是否有对应的远程分支。

如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做git pull合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用–force选项。

	$ git push --force origin

上面命令使用–force选项，结果导致在远程主机产生一个”非直进式”的合并(non-fast-forward merge)。除非你很确定要这样做，否则应该尽量避免使用–force选项。

最后，git push不会推送标签(tag)，除非使用–tags选项。

	$ git push origin --tags

<a name="git_remote"/>
### git remote([返回目录](#catalog))
为了便于管理，Git 要求每个远程主机都必须指定一个主机名，git remote 命令就用于管理主机名。
克隆版本库的时候，所使用的远程主机自动被 Git 命名为`origin`，当然也可以自定义。
不带选项的时候，git remote 命令列出所有远程主机。

	$ git remote
	# -v 参数可以获得更详细的信息
	$ git remote -v

`git remote show`命令加上主机名，可以查看该主机的详细信息。

	$ git remote show osc

`git remote add`命令用于添加远程主机。

	$ git remote add git@git.oschina.net:liguoying/Document.git

`git remote rm`命令用于删除远程主机。

	$ git remote rm osc

`git remote rename`命令用于远程主机的改名。

	$ git remote rename <原主机名> <新主机名>

<a name="git_stash"/>
### git stash([返回目录](#catalog))

	$ git stash

**说明：**
>备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。


	$ git stash list

**说明：**
>显示Git栈内的所有备份，可以利用这个列表来决定从那个地方恢复。


	$ git stash pop

**说明：**
>从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。由于可能存在多个Stash的内容，所以用栈来管理，pop会从最近的一个stash中读取内容并恢复。


	$ git stash apply stash@{n} 
	# n 为正整数

**说明：**
>从 Git 栈中取出指定的内容来恢复工作区的内容。


	$ git stash clear

**说明：**
>清空Git栈。此时使用gitg等图形化工具会发现，原来stash的哪些节点都消失了。

<a name="git_rebase"/>
### git rebase([返回目录](#catalog))

	# git rebase <分支名>
	git rebase master

**说明：**
>在 <font color="red">**master**</font> 分支上重做 <font color="red">**当前分支**</font> 的所有 <font color="red">**commit**</font>。<br/>

	# git rebase <branch1> <branch2>
	git rebase master develop

**说明：**
>在 <font color="red">**master**</font> 分支上重做 <font color="red">**develop**</font> 分支的所有 <font color="red">**commit**</font>。

**Git如何在分支 master 的基础上重做另分支 develop 的所有 commit 呢？**
>2个分支一定有一个相同的 <font color="red">**parent commit**</font> ，git会将 <font color="red">**develop**</font> 回滚至 <font color="red">**parent commit**</font>  处，并将 <font color="red">**develop**</font> 的历次 commit 以 <font color="red">**补丁(patch)**</font> 的形式保存下来，然后在 <font color="red">**master**</font> 分支上依次应用 <font color="red">**补丁(patch)**</font> 。

**rebase 的过程中出现冲突怎么办？**
>应用 <font color="red">**补丁(patch)**</font> 的过程中出现冲突是很正常的，这时候 git 会停止 rebase，提示你有冲突需要解决（解决这类冲突与解决其他冲突完全一样），解决完冲突之后无需自己 commit，而是调用以下命令让 git 继续 rebase：<br/>
> <font color="red">**git rebase --continue**</font> 