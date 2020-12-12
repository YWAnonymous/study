## git使用流程

### 1.git上传项目

```markdown
# 本地项目上传到git
git init       /*先初始化git环境*/
git commit -m "first commit"
git remote add origin https://github.com/YWAnonymous/spring.git
	git remote add 添加远程仓库 origin是远程仓库名称
git pull origin master
git branch -M master
	git push -u origin master
git push https://github.com/YWAnonymous/spring.git master:master
# 建立分支
git checkout -b login       //创建分支，并把代码复制到分支上
git push -u origin login	//如果Git上没有这个分支，推送分支
```

### 2.使用过程中报错并解决

```markdown
# 本地仓库在想做同步远程仓库到本地为之后本地仓库推送到远程仓库做准备时报错了
fatal: refusing to merge unrelated histories（拒绝合并不相关的历史）*
* 分析过程 *
	git remote -v 查看远程仓库
显示：
	origin https://XXXXXXXXXXX(fetch)
	origin https://XXXXXXXXXXX(push)
** 原因：在于本地仓库和远程仓库实际上是独立的两个仓库**
解决方法：
	git pull origin master --allow-unrelated-histories
以上是将远程仓库的文件拉取到本地仓库了。
紧接着将本地仓库的提交推送到远程github仓库上。

	git push <远程主机名> <本地分支名>:<远程分支名>
	git push origin master:master
```

### 3.git指令

```markdown
git status
git branch login(分支名)
git branch -a     ---查看所有分支
git checkout login(分支名)  ----切换到分支

git merge --no-ff -m 'xxx' login(分支名)  ---合并代码
发现冲突文件，解决冲突，在提交文件
git diff login master   ---比较两个分支
```

### 4.合并分支

```markdown
git branch
git checkout -b login       //创建分支，并把代码复制到分支上
git status
git add .
git commit -m '提交'
git push -u origin login	//如果Git上没有这个分支，使用这个命令
git checkout master
git merge login
git push        //如果git上有分支，使用这个命令
```



