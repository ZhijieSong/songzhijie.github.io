---
layout: post
title:  "Windows 包管理器 Chocolatey"
author: seven
categories: [ Windows ]
image: https://www.chocolatey.org/content/images/logo_square.svg
tags: [windows, chocolatey, pakage management]
---

最近在通过github pages搭建自己的博客，这过程中遇到了一个问题，就是博客前端工程的开发环境是要依赖ruby开发环境的，而我在windows下下载ruby的安装包是怎么也下不下来，回想过去搭建开发环境的过程，这种问题已经不是一次两次的问题了，需要下载各种各样的安装包并安装一直困扰着开发人员，甚至很多时候我们开玩笑说，安装环境搭建完成时，这个项目就已经完成一半了。在这个时候，作为一个windows开发人员是非常希望能有linux或mac ios 那样的命令行一键劲爽安装体验的，而我们今天所讲的Chocolatey就是这样一个工具，帮助我们通过命令行（或powershell）进行windows包管理，让我们不再需要执行从网页搜索安装包、下载、在本地安装这样的繁琐步骤，只需要一行命令就能完成整个安装操作。下面我们就来讲一下Chocolatey的使用方法吧。
# 安装
## 准备环境
1. 确保你的操作系统是Windows 7 或 Windows Server 2003及以上的版本 
2. 安装前首先要确定一件事情，那就是你电脑上要有.net framework 4.0以上的安装环境，作为一个windows开发，相信你对它肯定不陌生，如果不了解的话，那你需要自行搜索一下安装方法并进行安装。
3. 最后一项，你的电脑上需要有powerShell ，版本在v2以上。需要注意的是， 默认情况下powershell的Get-ExecutionPolicy命令是被Restricted，需要执行下列命令开启权限：
```powershell
Set-ExecutionPolicy AllSigned
```
或：
```powershell
Set-ExecutionPolicy Bypass -Scope Process
```

## 正式安装
准备好安装环境，安装就很简单了，你只需要复制如下命令到你的powershell窗口，enter执行即可
```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```
执行上述命令后，等待执行结束，如果没有报异常就说明安装成功了，这个时候既可以通过`choco`命令检查是否有问题。还可以通过`choco -?`查询使用帮助。
# 使用
Chocolatey命令还是比较丰富的，但对于我们来说最为常用的命令只有`list\search`,`find`, `install`, `info`, `upgrade`, `uninstall`, `version`，只要掌握了这几个命令基本就可以方便的使用了。 下面我们对这几个命令稍作介绍：
- **list\search**    用来搜索chocolatey库中的软件，如果你用过docker，一定不会陌生
- **install**   安装所要安装的软件，通过`-version`参数指定版本号
- **info**   用来查看所安装软件的信息，包括版本、名称、来源等
- **upgrade** 更新软件
- **uninstall**  卸载软件
- **version** 查看软件版本

关于安装包的类型可以参考如下介绍：
> Chocolatey 的包有不同的类型，有些包的名字里面会包含特殊的后缀，比如 .install ，.commandline，.portable ，有些包的名字不带这些后缀。

>安装带 .install 后缀的包，这个包会出现在系统控制面板里的 卸载或更改程序 里面，你可以把 .install 的包想成是通过安装程序（msi）安装的包。

>.commandline（未来会被抛弃） 与 .portable 后缀的包是压缩包（zip），安装这种后缀的包，你不能在 卸载或更改程序 里找到它们。

>你也可以选择不带后缀的包，这样如果系统中已经安装了这个包，就会跳过去，如果没安装，Chocolatey 就会为你安装一个，默认安装的这个包的类型应该就是 .install 后缀的包。

到此为止，Chocolatey的基本使用就介绍完了，如果需要更详细的功能，可以直接通过-h命令查看帮助信息，或直接通过搜索引擎进入官网查看帮助手册。

**参考文章**：[Windows下Chocolatey上手教程][上手教程]

[上手教程]:https://www.jianshu.com/p/5325decea0d2
