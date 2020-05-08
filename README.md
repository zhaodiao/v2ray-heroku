# V2Ray Heroku

## 重要

**由于 V2Ray 修改了自动安装脚本，导致该镜像在 Heroku 无法运行。请在 2020 年 2 月 20 日前部署过本镜像的用户，删除原先的应用，并重新在 Heroku 部署本镜像。**

**已经 Fork 过本项目的用户，请重新 Fork 一次。**

## 概述

用于在 Heroku 上部署 V2Ray Websocket。

**Heroku 为我们提供了免费的容器服务，我们不应该滥用它，所以本项目不宜做为长期翻墙使用。**

**可以部署两个以上的应用，实现[负载均衡](https://toutyrater.github.io/app/balance.html)，避免长时间大流量连接某一应用而被 Heroku 判定为滥用。**

**Heroku 的网络并不稳定，部署前请三思。**

## 镜像

本镜像仅 6MB，比起其他用于 Heroku 的 V2Ray 镜像，不会因为大量占用资源而被封号。

[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://dashboard.heroku.com/new-app?template=https://github.com/zhaodiao/v2ray-heroku)

## ENV 设定

### UUID

`UUID` > `一个 UUID，供用户连接时验证身份使用`。

## 注意

WebSocket 路径为 /。

AlterID 为 64。

80端口和443端口都可以使用，80端口速度普遍比443端口略快；443端口需要打开TLS，比80端口较安全。

服务端开启了DoH，本地客户端DNS服务器可选择`https://1.1.1.1/dns-query,https://rubyfish.cn/dns-query,https://dns.alidns.com/dns-query`这个是可选项，客户端可不做修改。

V2Ray 将在部署时自动安装最新版本。

**出于安全考量，除非使用 CDN，否则请不要使用自定义域名，而使用 Heroku 分配的二级域名，以实现 V2Ray Websocket + TLS。**


## 部署V2Ray到Heroku
既然都有GitHub和Heroku账号了，那么把V2Ray部署到Heroku的方法也一并分享出来。

### 从Git部署到Heroku
点开下面GitHub网站，会看到紫色Deploy to Heroku，点击会跳转到heroku app创建页面，填上app的名字，然后换上从 https://www.uuidgenerator.net/ 拷贝过来的UUID，点击下面deploy创建APP完成。

[deploy](https://dashboard.heroku.com/new-app?template=https://github.com/zhaodiao/v2ray-heroku)

需要记下的是appname,和你填入的UUID，下面就可以设置客户端翻墙了。

### 客户端设置
首先从[V2Ray官网](https://github.com/v2ray/v2ray-core/releases)下载软件

客户端config.json文件配置：
```
{
  "inbounds": [
    {
      "port": 1080,
      "listen": "127.0.0.1",
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth",
        "udp": false
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "appname.herokuapp.com",  //域名
            "port": 443,
            "users": [
              {
                "id": "b831381d-6324-4d53-ad4f-8cda48b30811",
                "alterId": 64
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "security": "tls",
        "wsSettings": {
          "path": "/"  //路径
        }
      }
    }
  ]
}
```
上面看不明白的，下面有客户端白话设置

### 80端口设置，不需要打开tls，如下：
```
地址(address) : appname.herokuapp.com	//appname替换成你的app名字

端口(port) : 80

用户ID(uuid) : 这里填写刚申请的UUID

额外ID(alterid) : 64

加密方式(security) : auto

传输协议(network) : ws

路径(type) : /
```
### 443端口设置，443端口需要打开底层tls传输，如下：
```
地址(address) : appname.herokuapp.com	//appname替换成你的app名字

端口(port) : 443

用户ID(uuid) : 这里填写刚申请的UUID

额外ID(alterid) : 64

加密方式(security) : auto

传输协议(network) : ws

伪装域名(host) : appname.herokuapp.com //不填或者填上你的herokuapp域名，可选项

路径(type) : /

底层传输 ：tls
```
到这也就完成了，这时候应该你就可以自由浏览网络了！


来源 https://yeahwu.com/posts/goproxy_heroku_proxy.html

