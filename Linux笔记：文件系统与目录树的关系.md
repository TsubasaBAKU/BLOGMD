---
title: Linux笔记：文件系统与目录树的关系
date: 2016-06-14 21:15:30
tags:
	- Linux
	- 文件系统
	- 目录树
categories:
	- LINUX
---
我们知道在Linux系统下，每个文都会有一个inode，且可根据文件大小来分配多个block来文件使用。我们知道目录实际上记录的是文件名，一般文件才是记录内容的地方。那么目录与文件在ext2文件系统当中是如何记录数据的呢？
<!--more-->

# 目录
当我们在Linux下的ext2文件系统中新建一个目录时，ext2会分配一个inode与至少一块block给该目录。其中，inode记录该目录的相关权限与属性，并可记录分配到的那块block号码；而block中这是记录在这个目录下的文件名与该文件名占用的inode号码。

# 文件
当我们在Linux下的ext2文件系统中新建一个一般文件时，ext2会分配一个inode与相对应文件大小数量的block给文件。例如，假设一个block快大小为4KB，我们要新建一个100KB的文件，那么就会分配一个inode与25个block来存储文件。但同时要注意，由于inode仅有12个直接指向，因此还需要多一个block来记录块号码。

# 目录树读取
目录树是从根目录赌气，因此系统通过挂载的信息可以找到挂载点的inode号码（通常一个文件系统的最顶层inode号码是由2号开始），此时就能够得到根目录的inode内容，并进而得到根目录的block内容，再一层一层的往下读到正确的文件名。

举例来说，如果想要读取/etc/passwd这个文件时，读取过程如下：

1. /的inode。通过挂载信息找到inode为2的根目录inode，且inode具有的权限可以让我们读到该block的内容（r与x）。
2. /的block。经由上个步骤获取到block的号码，找到该内容中又etc/目录的inode号码。
3. etc的inode。通过上个步骤得到该inode，读取其中的block。
4. etc的block。在该block中找到内容中含有passwd的inode。
5. passwd的inode。由上一步得到该inode的号码，进而读取block。
6. passwd的block。读取block的内容。

