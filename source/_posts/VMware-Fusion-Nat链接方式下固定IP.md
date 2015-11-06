title: VMware Fusion Nat链接方式下固定IP
date: 2015-08-26 11:21:41
tags:  VMWareFusion 虚拟机 固定IP
---
因为一些特别原因，在公司开发时只能虚拟机只能使用Nat链接方式，如果使用桥接方式，不同ap无法访问到我电脑内的虚拟机。只能通过端口转发方式，让别人的电脑访问我虚拟机做测试。

可是每次启动电脑时，虚拟机内的ip地址都会变。VMware Fusion并没有界面化的设置，需要在命令行中进行设置。

## 设置端口转发

```shell
cd /Library/Preferences/VMware Fusion/vmnet8
sudo vim nat.conf
```
找到
```shell
[incomingtcp]

# Use these with care - anyone can enter into your VM through these...
# The format and example are as follows:
#<external port number> = <VM's IP address>:<VM's port number>
```
在下面添加一行
```shell
// 9000是我本地转发的端口，假设本机IP地址:192.168.1.82，那么同事测试时访问192.168.1.82:9000即可访问到虚拟机
// 9000端口的请求，转发到虚拟机的80端口上
9000 = 172.16.59.131:80
```
到这一步虚拟机端口转发就做好了，`Esc`+`ZZ`或者`Esc`+`wq`保存并退出。
<!--more-->
到这里虽然设置好了端口转发，但是并没有应用上，需要重启服务或者虚拟机才可以。
通过一下命令可以直接重启服务
```shell
sudo /Applications/VMware\ Fusion.app/Contents/Library/vmnet-cli --stop
sudo /Applications/VMware\ Fusion.app/Contents/Library/vmnet-cli --start
```

到这一步端口转发生效，不过如果重启电脑的话，虚拟机的ip地址还是有可能会变的。

## 固定虚拟机ip地址

还是在`/Library/Preferences/VMware Fusion/vmnet8`目录下，找到`dhcpd.conf`文件。
```shell
vim dhcpd.conf
```
将会看到这样一段内容，它描述了通过DHCP分配ip地址的设置
```shell
subnet 172.16.59.0 netmask 255.255.255.0 {// 子网掩码
        range 172.16.59.128 172.16.59.254;// 网络段处于128~254之间，也就是说虚拟机的ip地址只可能被分配到其中的一个
        option broadcast-address 172.16.59.255;// 广播地址，无法被分配
        option domain-name-servers 172.16.59.2;// DNS地址
        option domain-name localdomain;
        default-lease-time 1800;                # default is 30 minutes
        max-lease-time 7200;                    # default is 2 hours
        option netbios-name-servers 172.16.59.2;
        option routers 172.16.59.2;
}
```
记下这几个有注释的几个地址，进入虚拟机。
我的虚拟机是win7。
打开`TCP/IPv4`属性设置，如下图：
![设置][1]
- IP地址处于`128~254`之间的任何一个即可
- 子网掩码和上面配置文件里要一致
- 默认网关是设置中的广播地址
- 下面的DNS地址一定要写，不然没法上网，一样是和配置文件中的一样
设置好之后，默认会重新选择一下网络环境是`家庭`、`工作`还是`公共`等。

至此，虚拟机ip地址就固定下来了。


  [1]: http://i3.tietuku.com/bada1525b352c50a.png
