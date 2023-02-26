## 问题

**origin / master与origin master**

这里实际上有三件事：`origin master`是两件事，`origin/master`是一件事。共计三件事

- `origin master`： 远程仓库(origin代表远程仓库)上的master分支

- `origin/master`：本地分支，是从远程拉取代码后，在本地建立的一份拷贝

  

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



