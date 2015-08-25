title: "安装hexo需要注意的一些坑"
date: 2015-03-26 02:02:19
tags: hexo
---

前言：
    
    因为电脑系统问题，整个盘格式化了，之前配置的hexo都忘记上传到网盘上。
    现在放在百度云盘的目录下，会自动上传。
    但是，部署hexo的时候，要关掉百度上传，不然会出现文件读取错误。
    这里的所有坑都在windows平台下，linux下的同学可以先去外边玩耍了。

# 前期准备的坑
	为了能够使用hexo，需要先安装 nodejs , npm ， hexo , git 。

## 安装nodejs
截止现在nodejs版本为0.12.1，其中中间版本12(偶数)代表是稳定版，1是小版本号。
    
安装nodejs只需要到官网上下载msi安装包即可，自动安装，自动写入PATH环境变量，很便利有木有~
    
这时，请按下 `开始按钮 + r` ，出现运行窗口，在里面输入 `powershell` ，然后回车出现蓝底的命令行界面。
    
在里面输入 `node -v` ，将会返回 `v0.12.1` ，这是我安装在电脑中nodejs目前的版本号。
    
如果你很快就到了这一步，那么恭喜你，你运气不错，因为到这里才开始有坑出现。
    
客官请看下面。
    
## npm
先有个好消息要告诉你：安装nodejs的时候，npm会自动帮你装好，并且也已经写入到系统的环境变量（写入环境变量是为了可以在命令行中调用）当中。
    
同样，和上面一样，你也可以在powershell中输入 `npm -v`，将会显示npm的版本号。
    
当然，有好消息也会有坏消息。

## Hexo
终于来到了今天的主题，Hexo.

要安装Hexo，比较推荐的一个做法是使用 `npm` 或者 `cnpm` 来安装，命令如下：
```
npm install hexo -g

// 或者
cnpm install hexo -g
```
这里的 `-g` 是指定为全局，全局安装之后，在任何地方都可以调用，如果不加入 `-g` 的话，包会被下载到在当前目录下，只可以被当前目录以内的程序访问。具体可以去了解nodejs中 `require` 的行为。

通常用npm安装包都会比较慢，因为npm的服务器在地球的另一边米国。数据传输需要通过海底电缆，因此速度非常慢，像我这样本命年运气不好的在请求的时候直接就超时了。
    
在使用npm安装包的时候，可以指定远程仓库的地址（默认在米国的那个）。在这种时候，我们可以为此次下载包指定一个远程仓库。
    
在国内淘宝架设了一个 [地址](https://npm.taobao.org)。

在官网上的命令步骤是：
打开 `cmd` 或者 `powershell`，切换到你想建立文件夹的地方。使用下面的命令，就会在当前目录下，建立一个 `blog` 命名的目录，这个目录下面在初始化的时候，会已经存在一些文件。
其中就包括 `package.json` 这个文件，在这个目录下执行 `npm install` 或者 `npm i` 的时候，`npm` 会自动寻找当前目录下的 `package.json` 文件，然后读取里面的依赖信息向远程仓库请求需要下载模块。
具体命令如下：
```
hexo init blog //(名字随意)
cd blog
npm install
```
然而，由于npm默认设置的仓库在米国，路途遥远，中间又有很多劫道的，在下载包的时候经常会超时或者出错。

在这个时候，可以在执行 `npm install` 或者 `npm i` 命令的时候，为其指定一个远程仓库。命令如下：
```
npm install --registry=https://registry.npm.taobao.org
```
当然，也可以这样写
```
npm i --registry=https://registry.npm.taobao.org
```
npm的安装命令，后面有 `install` | `i` 的时候，后面如果没有指定包的名字，那么会自动读取目录下的 `pachage.json` 文件来获取依赖信息，如果该目录下没有 `package.json` 文件，那么就会提示 `Couldn't read dependencies` 。

每次都指定这个地址多麻烦，所以也可以直接安装 `npm` 的中国兄弟 `cnpm` ：
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
这东西和 `npm` 基本差不多，所以安装好之后，以后依赖包的安装都可以直接使用 `cnpm` 命令来代替。

淘宝的npm仓库只是镜像，它每天会和远在米国的npm总库同步，因此，也会有一个新包在cnpm上找不到的情况，或者版本落后于最新版本的情况发生。

# 配置

## _config.yml
配置 `_config.yml` 文件，只要记住一件事：
```yml
# Site
title:沧浪 //错误
```
```
# Site
title: 沧浪 //正确
```
两者之间的唯一区别就是分好后面需要一个空格。
so，出现问题请先检查空格问题。

另外一个小坑是，大部分人使用Hexo的时候，都是为了放在 github 上面，所以，在配置 `_config.yml` 时候，有一项：
```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git // 注意这里 ， 必须写git，不可以写github，因为这里指的方式。
  repo: git@github.com:sinoon/sinoon.github.io.git
```
有些人辛辛苦苦的配置了密钥，但是在使用 `hexo d` 部署的时候，还是需要输入账号密码，原因在于使用了 `http` 协议。
其实就是说，上面的 `repo` 里面，要像我一样使用 `git` 协议。

## 发布
有些人不喜欢看doc文档，在部署的时候会提示 `ERROR Deployer not found:` ，原因可能如上面所说。

但是即使按照上面配置了也是会有问题的，原因在于需要安装一个 `hexo` 控件。命令如下：
```
npm install hexo-deployer-git --save
```

到了这一步，基本安装就完成了。