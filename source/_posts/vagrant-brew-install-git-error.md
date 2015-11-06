title: vagrant brew install git error
date: 2015-09-13 14:07:32
tags: vagrant brew-git-install-brew
---
## 前言
前几天因为需要在`php5.4`、`Mysql`环境中开发，所以准备搞一个虚拟机，跑一个`vagrant`。

今天安装`git`的时候，感慨`Mac OS`下有`homebrew`，随便一搜，发现`linux`下确实也有`brew`，一个`homebrew`的分支`linuxbrew`。

安装非常简单。

## 安装
首先身平台自带的安装工具`yum`或者`apt-get`安装依赖。
### Debian or Ubuntu

```sh
sudo apt-get install build-essential curl git m4 ruby texinfo libbz2-dev libcurl4-openssl-dev libexpat-dev libncurses-dev zlib1g-dev
```

### Fedora, CentOS or Red Hat

```sh
sudo yum groupinstall 'Development Tools' && sudo yum install curl git irb m4 ruby texinfo bzip2-devel curl-devel expat-devel ncurses-devel zlib-devel
```

之后，运行安装脚本就好了。
``` sh
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/linuxbrew/go/install)"
```
<!--more-->
由于网络原因，在安装速度取决与当前环境连接`github`的速度。

在安装的最后一步会提示输入`password`，`vagrant`的虚拟机实例默认的`password`是`vagrant`。

## 配置
安装好`linuxbrew`之后，需要把`shell`的环境设置一下，不然打`brew`的时候会提示找不到命令。
把下面的内容添加到`.bashrc` or `.zshrc`:

```sh
export PATH="$HOME/.linuxbrew/bin:$PATH"
export MANPATH="$HOME/.linuxbrew/share/man:$MANPATH"
export INFOPATH="$HOME/.linuxbrew/share/info:$INFOPATH"
```
这里也有一点需要注意，这三行需要放在最后一行。不然会无效的。

添加好之后，可以选择运行`sourse .bashrc`或者`sourse .zshrc`来重新加载配置，或者重新登录`shell`。

到这里，`linuxbrew`就安装好了，`brew`运行一下试试吧。

## 安装git报错
我一直很喜欢最新版本，`ubuntu`下默认安装的`git`版本是`1.9.1`。所以，安装好`linuxbrew`之后的第一步，就是通过`brew`再安装一次`git`，替换掉现有的`git`。

```sh
brew install git`
```

`brew`最厉害的地方就在于，它会自动分析依赖，然后安装响应的东西，不需要人工处理繁复的依赖。

但是在`vagrant`下直接运行上面的命令会报错：
```
tmp/tcl-tk--tk20150913-17770-afpei5/tk8.6.4/unix/../generic/tk.h:96:25: fatal error: X11/Xlib.h: No such file or directory
 #   include <X11/Xlib.h>
                         ^
compilation terminated.
make: *** [tkStubLib.o] Error 1

```
原因在于`vagrant`是无界面的，所以没有`X11`相关的东西。

理论上的解决方案自然是不安装`X11`有关的依赖，只是我不知道其中的命令。

我推测这个问题可能比较小众，因为`linuxbrew`上才1K出头的`start`，知道的人可能不多，再用在`vagrant`下的人恐怕就更少了。

搜了一圈在`linuxbrew`的`issue`中找到了答案：
```sh
brew install tcl-tk --without-tk
```

先这样安装`tcl`的依赖，然后再安装`git`就好了。

当然，最好在安装之前先`brew update`或者`brew up`一下。

