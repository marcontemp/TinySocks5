# TinySocks5
Python实现的极简的socks5服务端

[项目地址](https://github.com/woodyxiong/TinySocks5)

## 项目介绍
本项目是基于`socks5`协议的代理服务端，突破本地的网络限制。本项目只支持纯生的`socks5`协议，传递的信息都为明文传递，无任何加密。如果轻量级使用仅需在浏览器端安装`SwitchyOmega`等可以配置`socks5`连接的插件即可快速配置进行代理。如果为代理重度使用者请移步[shadowsocks](https://github.com/shadowsocks)或[shadowsocksr](https://github.com/shadowsocksr-backup)。

## 使用说明
### 服务端安装环境
Python3.6及以上(windows端和ubuntu端可完美运行)，跑的是本项目的Python脚本。
```
git clone https://github.com/woodyxiong/TinySocks5.git
python main.py -p [port]
```
### 客户端安装配置
首先安装`SwitchyOmega`插件

[Chrome/Firefox安装操作](https://switchyomega.com/download.html)

国产浏览器操作如下(以qq浏览器为例)

> 打开应用中心并下载`SwitchyOmega`

点击应用中心按钮并进入应用中心

![qq浏览器截图](http://qncdn.gfkui.cn/18/6/21qq%E6%B5%8F%E8%A7%88%E5%99%A8%E6%88%AA%E5%9B%BE.png)

从应用中心安装`SwitchyOmega`

![安装switchyomega](http://qncdn.gfkui.cn/18/6/21%E5%AE%89%E8%A3%85switchyomega.png)

> 配置代理

新建socks5代理，情景模式名称随意

![新建socks5代理](http://qncdn.gfkui.cn/18/6/21%E6%96%B0%E5%BB%BA%E4%BB%A3%E7%90%86.jpg)

配置代理 代理协议选择`socks5`，代理服务器填写部署的ip或者域名，代理端口填写代理服务器监听的端口

![配置代理](http://qncdn.gfkui.cn/18/6/21%E9%85%8D%E7%BD%AE%E4%BB%A3%E7%90%86.jpg)

进阶操作：配置自动模式

设置符合规则的进入`socks5`代理服务器，若没有进入规则则选择直接连接即可

规则列表网址为gfwlist项目
```
https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt
```

![配置自动模式](http://qncdn.gfkui.cn/18/6/21%E8%87%AA%E5%8A%A8%E6%A8%A1%E5%BC%8F.jpg)

最后点击自动模式完成配置

> 大功告成

![完成配置](http://qncdn.gfkui.cn/18/6/21%E7%BB%93%E6%9E%9C.jpg)

## 原理介绍
### 结构示意图如下

![Tinysocks5结构示意图](http://qncdn.gfkui.cn/18/6/21/TinySocks5.png)

### 文件简介

+ main.py           主函数入口文件
+ shell.py          分析以及输出shell指令
+ eventloop.py      程序的主循环
+ controller.py     程序控制器
+ tcprelay.py       内容分发类

### 原理分析
首先进入`main.py`，从这里分发到`shell.py`来解析端口、用户名、密码等配置信息。

再继续生成`Controller`和`EventLoop`类，`Controller`是`socks5`服务器端，用于监控浏览器发送的`socks5`请求，并且将监控的可读事件注册到`EventLoop`类对其进行监控。

`EventLoop`一直循环监控可读事件、可写事件、错误事件。

如果有浏览器客户端与`Controller`相连，则生成`Tcprelay`类，此类有`local_sock`用于与浏览器相连、`remote_sock`与目标服务器相连。并且将`local_sock`与`remote_sock`注册进`EventLoop`进行阻塞监视。

`Tcprelay`类用于处理浏览器的`socks5`协议的握手、验证以及连接目标服务器并且互相转发数据。

## Todo

- 更加灵活地处理错误
- 可以使用`docker`更加灵活的部署
- 守护进程模式
- 可以无缝热重启
- 在shell指令上添加可以指定用户名密码的功能
- 可以根据配置文件进行配置指令操作
