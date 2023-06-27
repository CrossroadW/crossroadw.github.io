linux中获取命令帮助的几种方式

```
#一个工具 help 是一个内置的 bash 命令，它为其他 bash 命令（echo、logout、pwd 等）提供帮助。
$ help echo

#对于其他可执行程序，通常有一个名为 --help 或类似名称的选项。
$ echo --help

#手册页是大多数 Linux 操作系统默认内置的手册。它们提供有关命令和系统其他方面的文档。
$ man ls

#描述来自每个命令的手册页。如果你运行 whatis cat，你会看到有一个带有简短描述的小简介。
$ whatis cat

### 语法
```
# info命令 
是Linux下info格式的帮助指令。
就内容来说，info页面比man page编写得要更好、更容易理解，也更友好，但man page使用起来确实要更容易得多。**一个man page只有一页**，而info页面几乎总是将它们的内容组织成多个区段（称为节点），每个区段也可能包含子区段（称为子节点）。理解这个命令的窍门就是不仅要学习如何在单独的Info页面中浏览导航，还要学习如何在节点和子节点之间切换。可能刚开始会一时很难在info页面的节点之间移动和找到你要的东西，真是具有讽刺意味：原本以为对于新手来说，某个东西比man命令会更好些，但实际上学习和使用起来更困难。

```bash
在info后面输入命令的名称就可以查看该命令的info帮助文档了：
info info
 N键： 显示（相对于本节点的）下一节点的文档内容。
 P键： 显示（相对于本节点的）前一节点的文档内容。
 H    显示帮助窗口
```
# man命令
手册页是大多数 Linux 操作系统默认**内置的手册**。它们提供有关命令和系统其他方面的文档。
```
$ man ls
```
