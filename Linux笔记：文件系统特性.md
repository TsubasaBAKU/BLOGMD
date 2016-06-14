---
title: Linux笔记：文件系统特性
date: 2016-06-14 12:44:30
tags:
	- Linux
	- 文件系统特性
categories:
	- LINUX
---
# 格式化
首先我们来谈谈格式化的概念。为什么需要进行“格式化”呢？
<!--more-->
这是因为每种操作系统所设置的文件属性、文件权限并不相同，为了存放这些文件所需的数据，因此就必须将分区进行格式为，以成为操作系统能够利用的文件系统格式。
我们也都知道，每种操作系统使用的文件系统并不相同，比如Windows操作系统支持FAT和NTFS，而Linux则支持ext2，在默认的情况下，Windows是不支持ext2文件系统的。

# 文件系统的运行
那么文件系统是如何运行的呢？这与操作系统文件数据有关。较新的操作系统文件数据除了文件实际内容之外，还有非常多的属性，例如Linux操作系统的文件权限和文件属性。**文件系统通常会把这两部分分开放置，即将文件属性与权限放置在inode中，而文件的实际内容放置在data block中。**这是一个非常重要的属性。另外，还有一个超级块，用来记录文件系统的整体信息，比如inode和data block的总量，剩余量等。

在这里我们总结一下：

1. super block：记录文件系统的整体信息，比如inode和data block的总量、使用量、剩余量等。
2. inode：每个inode都有编号，记录文件的属性，一个文件占用一个inode，同时记录此文件的数据所在的data block的号码。
3. data block：每个block都有编号，记录文件的实际内容，当一个文件太大时，会占用多个block。

由于每个inode与block都有编号，而inode中记录有相应文件的block编号，因此，如果能够找到对应文件的inode，就能找到文件的内容，就可以实际读出文件的所有数据了。我们用下图来解释说明下：
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/BLOGIMG/master/Linux%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F.png)
如上图，系统先格式化出inode与block。假设某一个文件的属性与权限是放置在4号inode，而这个inode中记录的block号码为2,7,13,15这四个号码，那么读取数据的方式就如同上图了。这种方式我们成为索引式文件系统。
FAT格式的文件系统并没有inode存在，所以FAT文件系统没有办法将这个文件在一开始就读出来，每个block号码都记录在前一个block当中，如下图：
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/BLOGIMG/master/FAT%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F.png)
假设我们文件的数据依次写入1,7,4,15这几个block中，但这个文件系统没有办法一口气读出这四个号码，它要一个个找出来，先找到1，再找到7，再找到4，再找到15.如果一个文件数据写入的block快分散的太过厉害，我们的磁盘将无法转一圈时就读到整个数据，要多转几圈才能读到完成内容。

经常会听到“碎片整理”，需要碎片整理的原因及就是文件写入的block快太过散了，此时读取性能会变的很差。这个时候可以通过碎片整理将同一个文件所属的block汇合在一起，这样数据的读取就变得比较容易。

# Linux的ext2文件系统

首先说，标准的Linux文件系统ext2就是使用以inode为基础的文件系统。文件系统一开始就将inode与block规划好了，除非重新格式化，或者利用高级命令改变文件系统大小，否则inode与block固定后大小就不在变动。但是如果仔细考虑一下，如果我们的文件系统高达数百GB时，那么将所有的inode与block放在一起是很不明智的决定，因为当两者数量过大时，不易于管理。

因此，**ext2文件系统在格式化的时候区分为多个块组（block group），每个块组都有独立的inode，block，superblock。**

**在文件系统前面有一个启动扇区（boot sector），这个启动扇区可以安装引导装载程序。**这是个非常重要的设计，如此一来我们就能够将不同的引导装载程序安装到别的启动扇区中，而不用覆盖整块硬盘唯一的MBR。如下图：
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/BLOGIMG/master/ext2%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F%E5%B1%9E%E6%80%A7.png)
这里我们做个总结：

1. data block：用来防止文件内容的地方，在ext2文件系统中所支持的block大小为1kb，2kb，4kb三种。在格式化时block大小就固定了，且每个block都有编号，以方便inode的记录。
2. 原则上block的大小与数量在格式化后就不能再变了，除非重新格式化。
3. 每个block只能放置一个文件的数据，如果文件小于block大小，则剩余空间不能够再被使用，如果文件大于block大小，则需要多个block来存储这个文件。
4. 每个inode大小固定均为128bytes。
5. 每个文件都只会占用一个inode，所以文件系统能够创建的文件数量与inode数量有关。
6. 系统读取文件时需要先找到inode，并分析inode所记录的权限与用户是否符合，若符合才能够开始实际读取block的内容。

我们来简略分析一下inode、block与文件大小的关系。inode要记录的数据非常多，但偏偏inode大小又只有128bytes而已，而记录每一个block号码又需要4byte，假设一个文件有400MB且每个block为4KB时，那么至少也需要10万条block号码的记录。inode哪有这么多记录的信息？为此我们系统很聪明地将inode记录block号码区域定义成12个直接、一个间接、一个双间接、与一个三间接记录区。所谓的间接就是再拿一个block当做记录block的号码。如图：
![cmd-markdown-logo](https://raw.githubusercontent.com/TsubasaBAKU/BLOGIMG/master/inode结构.png)

superblock记录了整个文件系统相关信息。此外每个blockgroup都可能含有superblock，但是该superblock就是作为第一个block group内super block的备份了，这样可以进行superblock的挽救。



