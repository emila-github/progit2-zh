## A3.12 底层命令

在本书中我们也遇到了不少底层的命令。

我们遇到的第一个底层命令是在 合并请求引用 中的 ls-remote 命令。我们用通过它来查看服务端的原始引用。

我们在 手动文件再合并、 Rerere 及 索引 章节中使用 ls-files 来查看暂存区的更原始的样子。

我们同样在 分支引用 一节中提到了 rev-parse 命令，它可以接受任意字符串，并将其转成一个对象的 SHA-1 值。

我们在 Git 内部原理 一章中对大部分的底层命令进行了介绍，这差不多正是这一章的重点所在。 我们尽量避免了在本书的其他部分使用这些命令。