<a name="catalog"/>
## 目录
* [Checkout confilct with files( pull without commit )](#q1)
* [Git Gui、Git Bash 显示中文乱码](#q2)
* [修改上一次提交的信息](#q3)
* [Merge Tool KDIFF3](#q4)

<a name="q1"/>
### 修改源文件后，没有commit，直接pull，导致"Checkout conflict with files" ([返回目录](#catalog))

**情景再现**
>自己从服务器获取最新代码后，开始自己的开发，中途同事更新了数据库表结构、项目的POJO类，我需要使用最新的代码，就打算从服务器`pull`最新的代码，这时问题来了：

**原因分析**
>从服务器拉取的最新代码与本地代码出现了冲突。

**解决方案**
>因为服务器的最新代码与本地代码出现了冲突，所以必须要先获取服务器的最新代码，再

<a name="q2"/>
### Git Gui、Git Bash 显示中文乱码 ([返回目录](#catalog))
1.修改`C:\Program Files\Git\etc\git-completion.bash`文件：

	alias ls='ls --show-control-chars --color=auto'

**说明:**
> 这其实是在起别名。<br/>
> 直接在这个文件里面添加这一句话即可。<br/>
> 但是注意不要添加到某些逻辑内部了，要作为单独一行添加。<br/>
> 使得在 Git Bash 中输入 ls 命令，可以正常显示中文文件名。

2.修改`C:\Program Files\Git\etc\inputrc`文件：

	set output-meta on
	set convert-meta off

 **说明：**
 > 只需要修改文件里面的这两句即可。<br/>使得在 Git Bash 中可以正常输入中文，比如中文的 commit log。

3.修改`C:\Program Files\Git\etc\profile`文件：
 
 	export LESSCHARSET=utf-8
 
 **说明**
 > 同样在文件里面添加这一句。<br/>
 > $ git log 命令不像其它 vcs 一样，n 条 log 从头滚到底，它会恰当地停在第一页，按 space 键再往后翻页，这是通过将 log 送给 less 处理实现的。<br/>
 > 以上即是设置 less 的字符编码，使得 $ git log 可以正常显示中文。<br/>
 > 其实，它的值不一定要设置为 utf-8，比如 latin1 也可以……。<br/>
 > 还有个办法是 $ git –no-pager log，在选项里禁止分页，则无需设置上面的选项。

4.Git Gui 中文乱码
 
	编辑 --> 选项 --> Default File Content Encoding --> UTF-8
 

<a name="q3"/>
### 修改上一次提交的信息 ([返回目录](#catalog))

	git commit --amend


**说明：**
> 该命令是在修改上次提交的内容，它会把当前已暂存的内容和上次提交的内容作为一次提交，并允许修改提交信息。

<a name="q4"/>
### Merge Tool KDIFF3([返回目录](#catalog))
**1.Download and install kdiff3**

**2.Set kdiff3 path in Git**

	git config --global mergetool.kdiff3.path "E:\001liguoying\ProgramFiles\KDiff3\kdiff3.exe"

 > 通过以上方式指定的`kdiff3`的路径后，就可以使用以下命令来调用`kdiff3`：

	git mergetool -t kdiff3

**3.Don’t prompt before each invocation of kdiff3**

	git config --global mergetool.prompt false
	# 注意：不要写成 mergetool.kdiff3.prompt

 >运行 kdiff3 时，git不再给出<br/>
 >&nbsp;&nbsp;&nbsp;&nbsp;“Hit return to start merge resolution tool (kdiff3):”(敲击回车启动)<br/>
 >的提示。

**4.Set kdiff3 as a default conflict solution program while using command “git mergetool”**

	git config --global merge.tool kdiff3
 
>git mergetool 默认调用 merge.tool 参数指定的工具。