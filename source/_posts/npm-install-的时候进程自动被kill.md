title: npm install 的时候进程自动被kill
date: 2015-08-26 10:38:47
tags: npm nodejs install
---

今天在DigitalOcean的机器上，安装`blog`，打算作为自己的博客使用，不过在`npm install`的时候，总是被系统`kill`掉。

无奈因为买的最便宜的机器，内存只有500M，所以，只能使用虚拟内存使用。

这种被`kill`的情况，也会发生在开虚拟机的时候。

命令如下：
因为一般我登录DO的服务器都是直接用root身份的，这样说起来是不安全的，一个不小心容易出事。
所以，以下命令，因为登录的身份不同，是可以把sudo去掉的。
```sh
// 查看现在是否已经挂在了虚拟内存
// 空的是正常的
sudo swapon -s
```

创建一个1G的文件。
```sh
sudo fallocate -l 1G /swapfile
```

谨慎起见，看一下创建的对不对。
```sh
ls -lh /swapfile
```

设置文件权限，这一步可选。只是出于安全考虑，避免文件被其他用户随意读写。
```sh
sudo chmod 600 /swapfile
```

设置交换区域
```sh
sudo mkswap /swapfile
```

启用文件
```sh
sudo swapon /swapfile
```

这个时候，已经挂载上虚拟内存了，可以运行一下一开始的那个命令查看。
```sh
sudo swapon -s
```

虽然成功挂载了虚拟内存，但是还有一个问题就是下一次机器重启的时候不会自动挂载上。
需要使用下面的命令

```sh
// 打开 fstab 文件。
sudo vim /etc/fstab
```

```sh
// 告诉操作系统自动使用swap文件
/swapfile   none    swap    sw    0   0
```