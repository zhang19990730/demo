本项目转载自：https://github.com/bclswl0827/v2ray-heroku 的旧版本v2ray+vmess架构。

原作者近期变更项目转入v2ray+vless架构，但我还是想要旧版本v2ray+vmess架构。

为了方便，我在下面区镜像快捷方式更改为本转载项目的地址。才能调用这个转载项目的代码文件。

除了readme.md文件完善内容描述，其他的代码文件并无改动，感谢原作者的付出~

部署参考教程：https://www.rvich.com/archives/1362.html

# V2Ray Heroku

## 概述

用于在 Heroku 上部署 V2Ray Websocket。

**Heroku 为我们提供了免费的容器服务，我们不应该滥用它，所以本项目不宜做为长期翻墙使用。**

**可以部署两个以上的应用，实现 [负载均衡](https://toutyrater.github.io/routing/balance2.html)，避免长时间大流量连接某一应用而被 Heroku 判定为滥用。**

**Heroku 的网络并不稳定，部署前请三思。**

## 镜像

本镜像不会因为大量占用资源而被封号。

[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://dashboard.heroku.com/new?template=https%3A%2F%2Fgithub.com%2FTestOurView%2Fv2ray-heroku)

## ENV 设定

### UUID

`UUID` > `一个 UUID，供用户连接时验证身份使用`。

## cloudflare workers反代代码（二选一）：

方法一：

```
addEventListener(
    "fetch",event => {
        let url=new URL(event.request.url);
        url.hostname="你的heroku域名.herokuapp.com";
        let request=new Request(url,event.request);
        event. respondWith(
            fetch(request)
        )
    }
)
```

方法二（可部署两个一样的程序进行分流）：

```
const SingleDay = '应用程序名1.herokuapp.com'
const DoubleDay = '应用程序名2.herokuapp.com'
addEventListener(
    "fetch",event => {
        let nd = new Date();
        if (nd.getDate()%2) {
            host = SingleDay
        } else {
            host = DoubleDay
        }
        let url=new URL(event.request.url);
        url.hostname=host;
        let request=new Request(url,event.request);
        event. respondWith(
            fetch(request)
        )
    }
)
```

## cloudflare 自选ip：

参照项目: https://github.com/badafans/better-cloudflare-ip

## 客户端配置：

服务器地址：自选ip（如：icook.tw）

端口：443

默认UUID：ad806487-2d26-4636-98b6-ab85cc8521f7 (务必创建时自定义UUID码)

vmess额外id（alterid）：64

加密：auto

传输协议：ws

伪装类型：none

伪装host：****.workers.dev(CF Workers反代地址)

path路径：/ 

底层传输安全：tls

跳过证书验证：false

SNI地址：****.workers.dev(CF Workers反代地址)

V2Ray 将在部署时自动安装最新版本。

**出于安全考量，除非使用 CDN，否则请不要使用自定义域名，而使用 Heroku 分配的二级域名，以实现 V2Ray Websocket + TLS。**
