---
title: Windows下OpenVPN使用技巧
date: 2016-05-02 00:11:46
tags: ["OpenVPN"]
categories: ["Windows","Tools"]
---

> 最近入职公司需要用到OpenVPN连接工作网络，因为程序员都是懒惰的，所以折腾发现了一些技巧可以提高效率（其实是少点几下鼠标...），废话不多说，下面开始正题。

## 安装
这个没什么说的，官网地址下载安装即可，记得用管理员权限运行。
https://openvpn.net/index.php/open-source/downloads.html

## 运行
公司采用公钥认证，所以把公司给你的配置文件统统拷贝到 C:\Program Files\OpenVPN\config 目录下，其中包括证书，key，配置文件，如果有多个VPN网络记得改动文件名，然后也得修改OpenVPN配置文件中的文件名，这个网上有一大堆教程。
这时候你打开OpenVPN-GUI应该就可以连接到公司内网了。

## 免密码
公司的私钥使用了密码进行加密，所以连接时会提示输入密码，这个当然不能忍，首先新建一个`key.txt`，里面贴上你的密钥，然后打开你的OpenVPN配置文件，就是前面拷贝进config目录以`.ovpn`为后缀的文件，在最后添加一行`askpass key.txt`，保存重新打开客户端连接就不会提示输入密钥了。

## 同时使用多个连接
- 安装完默认只有一个虚拟网卡，如果需要同时连接多个VPN需要添加多个TAP虚拟网卡，你想要多少个网络就要添加多少个网卡，进入TAP网络目录，默认是C:\Program Files\TAP-Windows，不一样的自己找到对应目录，打开cmd运行命令`bin\tapinstall.exe install driver\OemVista.inf tap0901`，最后的tap0901对应于driver目录下的文件名，不一致自行修改。最后打开系统网络连接看看是否有两个TAP虚拟网卡，有即操作成功了，然后可以重命名网卡名称，如`TAP01`，没有自己检查下是否权限问题。这时候就可以测试下多个网络连接了。
- 修改VPN的配置文件，找到`dev-node`这一行，给每个网络分配好网卡，不能有冲突，如A.ovpn中为`dev-node TAP01`，B.ovpn中为`dev-node TAP02`

## 双击自动连接
打开OpenVPN快捷方式属性，在目标一栏最后加上`--connect A.ovpn`，可以指定多个配置文件同时拨号，如`--connect A.ovpn --connect B.ovpn`，注意和前面的路径有个空格，确定以后双击即可直接连接上两个VPN，如果还想开机启动的把快捷方式丢到启动目录吧。
