## 10.5 引用规格

纵观全书，我们已经使用过一些诸如远程分支到本地引用的简单映射方式，但这种映射可以更复杂。 假设你添加了这样一个远程版本库：

```
$ git remote add origin https://github.com/schacon/simplegit-progit
```

上述命令会在你的 .git/config 文件中添加一个小节，并在其中指定远程版本库的名称（origin）、URL 和一个用于获取操作的引用规格（refspec）：

```
[remote "origin"]
	url = https://github.com/schacon/simplegit-progit
	fetch = +refs/heads/*:refs/remotes/origin/*
```

引用规格的格式由一个可选的 + 号和紧随其后的 <src>:<dst> 组成，其中 <src> 是一个模式（pattern），代表远程版本库中的引用；<dst> 是那些远程引用在本地所对应的位置。 + 号告诉 Git 即使在不能快进的情况下也要（强制）更新引用。

默认情况下，引用规格由 git remote add 命令自动生成， Git 获取服务器中 refs/heads/ 下面的所有引用，并将它写入到本地的 refs/remotes/origin/ 中。 所以，如果服务器上有一个 master 分支，我们可以在本地通过下面这种方式来访问该分支上的提交记录：

```
$ git log origin/master
$ git log remotes/origin/master
$ git log refs/remotes/origin/master
```

上面的三个命令作用相同，因为 Git 会把它们都扩展成 refs/remotes/origin/master。

如果想让 Git 每次只拉取远程的 master 分支，而不是所有分支，可以把（引用规格的）获取那一行修改为：

```
fetch = +refs/heads/master:refs/remotes/origin/master
```

这仅是针对该远程版本库的 git fetch 操作的默认引用规格。 如果有某些只希望被执行一次的操作，我们也可以在命令行指定引用规格。 若要将远程的 master 分支拉到本地的 origin/mymaster 分支，可以运行：

```
$ git fetch origin master:refs/remotes/origin/mymaster
```

你也可以指定多个引用规格。 在命令行中，你可以按照如下的方式拉取多个分支：

```
$ git fetch origin master:refs/remotes/origin/mymaster \
	 topic:refs/remotes/origin/topic
From git@github.com:schacon/simplegit
 ! [rejected]        master     -> origin/mymaster  (non fast forward)
 * [new branch]      topic      -> origin/topic
```

在这个例子中，对 master 分支的拉取操作被拒绝，因为它不是一个可以快进的引用。 我们可以通过在引用规格之前指定 + 号来覆盖该规则。

你也可以在配置文件中指定多个用于获取操作的引用规格。 如果想在每次获取时都包括 master 和 experiment 分支，添加如下两行：

```
[remote "origin"]
	url = https://github.com/schacon/simplegit-progit
	fetch = +refs/heads/master:refs/remotes/origin/master
	fetch = +refs/heads/experiment:refs/remotes/origin/experiment
```

我们不能在模式中使用部分通配符，所以像下面这样的引用规格是不合法的：

```
fetch = +refs/heads/qa*:refs/remotes/origin/qa*
```

但我们可以使用命名空间（或目录）来达到类似目的。 假设你有一个 QA 团队，他们推送了一系列分支，同时你只想要获取 master 和 QA 团队的所有分支而不关心其他任何分支，那么可以使用如下配置：

```
[remote "origin"]
	url = https://github.com/schacon/simplegit-progit
	fetch = +refs/heads/master:refs/remotes/origin/master
	fetch = +refs/heads/qa/*:refs/remotes/origin/qa/*
```

如果项目的工作流很复杂，有 QA 团队推送分支、开发人员推送分支、集成团队推送并且在远程分支上展开协作，你就可以像这样（在本地）为这些分支创建各自的命名空间，非常方便。

### 引用规格推送

像上面这样从远程版本库获取已在命名空间中的引用当然很棒，但 QA 团队最初应该如何将他们的分支放入远程的 qa/ 命名空间呢？ 我们可以通过引用规格推送来完成这个任务。

如果 QA 团队想把他们的 master 分支推送到远程服务器的 qa/master 分支上，可以运行：

```
$ git push origin master:refs/heads/qa/master
```

如果他们希望 Git 每次运行 git push origin 时都像上面这样推送，可以在他们的配置文件中添加一条 push 值：

```
[remote "origin"]
	url = https://github.com/schacon/simplegit-progit
	fetch = +refs/heads/*:refs/remotes/origin/*
	push = refs/heads/master:refs/heads/qa/master
```

正如刚才所指出的，这会让 git push origin 默认把本地 master 分支推送到远程 qa/master 分支。

### 删除引用

你还可以借助类似下面的命令通过引用规格从远程服务器上删除引用：

```
$ git push origin :topic
```

因为引用规格（的格式）是 <src>:<dst>，所以上述命令把 <src> 留空，意味着把远程版本库的 topic 分支定义为空值，也就是删除它。