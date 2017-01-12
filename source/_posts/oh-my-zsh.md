title: 终极shell, 使用zsh代替bash
tags: 
- Shell 
- zsh
- oh-my-zsh
categories:
- Mac
- Tools
date: 2017/01/09 02:46
thumbnail: http://ohmyz.sh/img/themes/nebirhos.jpg
---
换到Mac下编码刚好快一年了,感受最大的就是开发效率的提升,使用越来越顺手了,不像最初的一两个星期, 没有了Windows的开妈菜单,总觉得不方便,痛苦一两周过去后,就不想再换到windows下编码了,虽然有时候为了测试万恶的IE浏览器,还有在电脑上安装几个版本的windows的虚拟机。
今天要说一下是用起来感觉最爽的神器zsh和oh-my-zsh。
那什么是zsh呢?

<!-- more -->

## zsh

Zsh 和bash一样,是一种shell。
在计算机科学中，Shell俗称壳（用来区别于核），是指“提供使用者使用界面”的软件（命令解析器）。它类似于DOS下的command和后来的cmd.exe。它接收用户命令，然后调用相应的应用程序。
Shell 一般分为两大类。
一：图形界面shell（Graphical User Interface shell 即 GUI shell）
例如：应用最为广泛的 Windows Explorer （微软的windows系列操作系统），还有也包括广为人知的 Linux shell，其中linux shell 包括 X window manager (BlackBox和FluxBox），以及功能更强大的CDE、GNOME、KDE、 XFCE。
二：命令行式shell（Command Line Interface shell ，即CLI shell）
例如：
bash / sh / ksh / csh（Unix/linux 系统）
（MS-DOS系统）
cmd.exe/ 命令提示字符（Windows NT 系统）
Windows PowerShell（支援 .NET Framework 技术的 Windows NT 系统）
**传统意义上的shell指的是命令行式的shell，以后如果不特别注明，shell是指命令行式的shell。**
目前常用的 Linux 系统和 OS X 系统的默认 Shell 都是 bash，但是真正强大的 Shell 是深藏不露的 zsh， 这货绝对是马车中的跑车，跑车中的飞行车，史称『终极 Shell』，但是由于配置过于复杂，所以初期无人问津，很多人跑过来看看 zsh 的配置指南，什么都不说转身就走了。直到有一天，国外有个穷极无聊的程序员开发出了一个能够让你快速上手的zsh项目，叫做「oh my zsh」，Github 网址是：[https://github.com/robbyrussell/oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)。这玩意就像「X天叫你学会 C++」系列，可以让你神功速成，而且是真的。

### 常用Shell命令
* cat 文件名 输出文件内容到基本输出（屏幕 or 加>fileName 到另一个文件）
* cb 格式化源代码
* chmod //change mode，改变文件的权限
* cp copy
* date 当前的时间和日期
* echo $abc 在变量赋值之后，只需在变量前面加一个$去引用.
* lint 语法检查程序
* ls dir
* man help
* more type
* du 查看磁盘空间状况
* ps 查看当前进程状况
* who 你的用户名和终端类型
* 定义变量 name=abc? (bash/pdksh) || set name = abc (tcsh)
* mkdir 创建目录
* rmdir 删除目录
* cd 进入目录
* rm 删除文件
* more 显示文件
* echo 显示指定文本
* mv 改文件名 /移动文件
* pwd 显示目录路径命令

### 安装zsh
* Mac: Mac OS X 中默认安装了zsh。
* Ubuntu：sudo apt-get install zsh


## oh-my-zsh

Oh-my-zsh 是一个开源的,由社区维护的配置zsh的框架。 它提供了非常非常多的有用的功能,插件,主题,还有一些让你兴奋的叫起来的东西...

上面这段话是Oh-my-zsh上抄过来的。原文是英文,由于本人英文差,翻过来很别扭,大家凑合着看。不过,它说明了oh-my-zsh是个什么东西。

### 安装oh-my-zsh
安装非常简单,常用的方式有两种
* curl安装 
```bash
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
* wget安装
```bash
$ sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```
### 自定义设置oh-my-zsh
安装完了默认就可以使用了, 不信可以打开你喜欢的terminal.app试试,看下,风格是不是变化了,当然，最主要的不是这个,主要的是各种智能化的提示和目录进入的方式,再不不停的输入cd ...了。当然,为了满足你可能有的种种变态的需求,oh-my-zsh也提供了非常的个性化的插件和主题,这些都在~/.oh-my-zsh目录中。如下图
![](/images/oh-my-zsh-plugins.png)
除了主题和插件外还有很多其它个性化设置, 具体参见:[https://github.com/robbyrussell/oh-my-zsh/wiki/Customization](https://github.com/robbyrussell/oh-my-zsh/wiki/Customization)
### oh-my-zsh插件

具体参见:[https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)

### oh-my-zsh主题
具体参见[https://github.com/robbyrussell/oh-my-zsh/wiki/Themes](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)