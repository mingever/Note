## 分支

远程仓库上有master和dev两个分支

1. 克隆

	``` bash
	git clone git@github.com:mingever/blog.git
	```

2. 查看所有分支

	``` bash
	git branch -a
	* main
		remotes/origin/HEAD -> origin/main
		remotes/origin/dev
		remotes/origin/main
	# 默认有了dev和master分支，所以会看到如下三个分支
	# master[本地主分支] origin/master[远程主分支] origin/dev[远程开发分支]
	# 新克隆下来的代码默认master和origin/master是关联的，也就是他们的代码保持同步
	# 但是origin/dev分支在本地没有任何的关联，所以我们无法在那里开发
	```


3. 创建本地关联origin/dev的分支

	``` bash
	git checkout dev origin/dev  
	# 创建本地分支dev，并且和远程origin/dev分支关联，本地dev分支的初始代码和远程的dev分支代码一样
	```
	
	> 自己测试发现直接用 `git checkout dev` 会直接创建dev分支，并关联到origin，然后本地自动切换到dev分支
	>
	> ``` bash
	> D:\blog [main ≡]> git checkout dev
	> Switched to a new branch 'dev'
	> branch 'dev' set up to track 'origin/dev'.
	> ```
	>
	> 发现直接git checkout dev origin/dev会报erro，这是因为本地没有dev分支，
	> 这个时候需要增加参数-b，`git checkout -b dev origin/dev`
	
4. 切换到dev分支进行开发

	``` bash
	git checkout dev  # 切换到dev分支，然后就是常规的开发
	```

5. 直接pull就是pull的dev的代码了，然后可以用push推送到dev分支

## 链接远程仓库

```shell
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:mingever/qingge_pure-design.git
git push -u origin main
```

## 问题

**origin / master与origin master**

这里实际上有三件事：`origin master`是两件事，`origin/master`是一件事。共计三件事

- `origin master`： 远程仓库(origin代表远程仓库)上的master分支

- `origin/master`：只代表一个概念，即远程分支名，本地分支，是从远程拉取代码后，在本地建立的一份拷贝

  

**删除remotes/origin/HEAD -> origin/master指针**

问题：

- 从github远程仓库克隆代码，默认情况下，当前指针将位于`remotes/origin/HEAD`，它是远程仓库引用的符号分支。

- 这里，master是本地存储库中的一个分支，`remotes/origin/master`是远程origin上名为master的分支。

- `origin/HEAD`就像一个指针，表示默认分支，它指向`origin/master`，即`origin/master`是默认分支，删掉也是可以的。

``` bash
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```

解决办法：

使用`git remote set-head origin -d`删除`origin/HEAD`符号引用，删除后效果如下

``` bash
* master
  remotes/origin/master
```



