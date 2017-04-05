这是一个管理 Ptunnel 的脚本，包括服务器到本地的全部操作都可以通过这个工具完成，目前完美支持 Debian/Ubuntu，CentOS/Fedora 不支持部分功能。

#### 功能：
* 支持服务器自动部署并启动。
* 服务端遇到意外可以自动重启。
* 支持本地自动部署并启动。
* 支持断线自动重连（选择性修复连接）。
* 提供直观的监视器，可以实时查看连接状态。
* 支持指定网卡分享 socks5 代理给他人。
* 支持指定 socks5 端口转发为 http 端口。
* 密码认证。

#### 待添加/修复功能：
* 支持自动修复 http 代理并允许指定 http 端口。
* 修复 CentOS/Fedora 下的连接超时判断问题。
* HTTP 代理自动修复。

```
$ ./Pshell.sh -h
------------------------------------------------------------------------------
   ____  _                          _   ____  _          _ _ 
  |  _ \| |_ _   _ _ __  _ __   ___| | / ___|| |__   ___| | |
  | |_) | __| | | | '_ \| '_ \ / _ \ | \___ \| '_ \ / _ \ | |
  |  __/| |_| |_| | | | | | | |  __/ |  ___) | | | |  __/ | |
  |_|    \__|\__,_|_| |_|_| |_|\___|_| |____/|_| |_|\___|_|_|
  Email: i@zuolan.me                  Blog: https://zuolan.me
------------------------------------------------------------------------------
  一个关于 Ptunnel 部署以及代理管理的脚本。不加参数直接运行脚本即可连接。
  可选参数   -  说明
------------------------------------------------------------------------------
  -a (--auto)      -  断线自动重连，自动修复断开的连接。
  -m (--monitor)   -  查看代理运行情况。
  -d (--driver)    -  使用 -d [enp3s0|wlp2s0|eth0|wlan0] 指定网卡(默认全部)。
  -p (--port)      -  选择本地 HTTP 代理端口（默认配置/etc/privoxy/config）。
  -k (--kill)      -  重启 sshd 进程（当 ssh 无法连接时使用）。
  -l (--local)     -  安装本地守护容器。
  -s (--server)    -  安装服务器守护进程。
  -h (--help)      -  显示帮助信息。详细说明阅读 README 文件。
```

## 第一步、服务器安装
执行 `sudo ./Pshell.sh -s` 即可自动安装并启动。

## 第二步、本地电脑安装
执行 `sudo ./Pshell.sh -l` 即可自动安装并运行。

## 第三步、填写配置文件
在运行脚本连接之前需要填写配置文件，模板如下。
新建一个文件，名为`proxy.list`，然后按照下面的模板填写你的配置。
```
节点名称:容器名称:容器端口:Socks5端口:服务器IP:密码
广州:gz:8001:10001:123.45.67.89:pass1
香港:hk:8002:10002:123.45.67.89:pass1
青岛:qd:8003:10003:123.45.67.89:pass2
东京:to:8004:10004:123.45.67.89:password
```
填完就可以启动了，但如果你想更详细定义脚本变量可以在脚本头部中设置。

## 第四步、直接运行
使用 `./Pshell.sh` 直接运行脚本，然后你可以使用配置文件中设置的 Socks5 端口连接到外网。设置方法和普通 Socks5 端口使用一样。（例如 Google Chrome 中的插件 SwitchyOmega。）

## 扩展一、Socks5 转 http
有些软件不支持 Socks5 代理协议，所以提供端口转换功。
使用 `./Pshell.sh -p <port>` 可以指定其中一个 socks5 端口转换为 http 端口（转换后为 8118）。
> 端口转换功能是保存起来的，不需要每次运行都指定它，除非你想重新指定转换的 socks5 端口。

## 扩展二、断线自动重连
ssh 连接有时候会因为各种问题产生断线情况，因此你可以使用 `./Pshell.sh -c` 参数来实现自动断线重连。
看视频时使用这个参数可以避免因为断线而导致视频加载失败。
```
$ ./Pshell.sh -c
 | 广州：连接正常 | 香港：已经修复 | 青岛：连接正常 | 东京：已经修复 | http 代理正常 |
```
~~不建议用这个方法来进行下载文件，因为很多网站下载文件时有自己的判定程序，不断重连会导致下载文件下载不完整。~~
> 使用自动重连过程中，所使用节点会一直处于"已经修复"状态，属于正常现象。

## 扩展二、分享 Socks5 端口
如果你想分享代理给他人用，可以使用 `./Pshell.sh -n <enp3s0>` 参数指定网卡分享 Socks5 端口。
> 常用的网卡有 enp3s0|wlp2s0|eth0|wlan0 这些，使用 ifconfig 命令可以查看。

也可以手动设置 Privoxy 的 8118 端口为对外开放，这样他人可以通过这个 http 端口访问外网。（这个功能以后会添加。）

## 扩展四、重启 sshd 进程
在使用过程中可能会出现 sshd 进程崩溃的情况，这时候明明没有连接异常但死活连不上。
这个时候你可以使用 `./Pshell.sh -k` 参数来杀死崩溃 sshd 进程并重新启动 sshd 进程。

## 最后
使用 alias 指定脚本为特定命令即可更加方便启动。
其他功能自己发现，其实也没什么其他功能了。在脚本中可以看到全部可选参数，有些没有写在帮助中。
