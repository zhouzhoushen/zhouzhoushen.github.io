---
date: '2024-04-13T23:38:32+08:00'
draft: false
title: '代理配置'
tags: ['Tools']
categories: ['General']
summary: ""
math: true
---

# 代理配置

这里介绍笔者科学上网的方式。

首先，我们要在不同的平台下下载不同的代理工具，它会帮我们转发流量到代理服务器。下面是笔者使用的代理工具

* Linux: V2rayA
* Windows: V2rayN
* Android: V2rayNG
* CLI: ProxyChains

关于纯命令行界面下ProxyChains的使用，请参见[这里](../深度学习/动手实践/环境准备.md#科学上网)。

其余三种工具的使用方式基本相同。

1. 访问其官网，遵循官方指示下载安装与配置。
2. 导入订阅连接。
3. 选择一个可用的代理服务器并启动。

以订阅连接提供的代理服务器较容易在Google上搜索到，例如[FlyingBird](https://flyingbird.cc)。

购买时需要注意两点。

* 代理服务器卖家有可能跑路，尽量高频率小额度短时间段购买。
* 可以在前述工具中测试所有代理服务器的连接延迟来判断代理服务器的连接质量。

还要注意的是，这三种工具不会转发命令行工具的流量。要使命令行程序执行时产生的流量通过这些工具转发，需要执行以下命令来设置控制特定协议转发代理的环境变量。以Windows系统为例：

* Command Prompt

```bash
set HTTPS_PROXY=socks://127.0.0.1:<port> 
set HTTP_PROXY=socks://127.0.0.1:<port>
```

* Bash

```bash
export https_proxy=socks://127.0.0.1:<port>
export http_proxy=socks://127.0.0.1:<port>
```

这里的\<port\>是代理工具中设置的对应特定协议的本地监听端口。

通用格式为：

```bash
# 环境变量名=协议://用户名:密码@地址:端口
export http_proxy=http://<proxy_username>:<proxy_password>@<your_proxy_server>:<your_proxy_port>
```

如果想要取消为命令行程序代理，执行`set https_proxy=`或`export https_proxy=`来重置相应环境变量即可。

## VMWare虚拟机在NAT模式下上不了网

笔者遇到了这个问题，在终端中执行`ifconfig`只显示`lo`环回网卡，所以虚拟机的有线网卡被关闭了。

笔者执行`nmcli device show`可以查看所有网卡的状态，果然有线网卡`ens33`的状态是关闭的。笔者执行`nmcli n on`启用`nmcli`工具，有线网卡`ens33`随着`nmcli`的启用而开启，然后就可以上网了。

## 不同订阅在不同平台的表现不一致

笔者目前购买了两个订阅。

ghelper的订阅连接在windows上的v2rayN导入后测试延迟全为-1，即全不可用，实际也不可用；而在linux上的v2rayA导入后测试延迟有些节点不为-1，这些节点可用。

flyingbird的订阅连接在windows上的v2rayN导入后许多节点都可用；而在linux上的v2rayA导入后全不可用。

这些机场的客服服务都非常简陋，基本没什么帮助，笔者在flyingbird的账号密码忘了也无法找回。

总结来说，在挑选机场时首先要谨慎，多看看各家的机场推荐列表；购买订阅时尽量小额购买，因为这些机场随时会跑路且基本无售后，客服非常水，完全解决不了问题。