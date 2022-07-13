## 注意事项
1）5月8日晚，CloudFlare Workers 的业务域名 Workers.dev 被防火长城 DNS 污染、SNI阻断。

2）CloudFlare Workers，可自定义workers域名，教程已更新至博客`ifts.ml`；经过添加自定义域名，更换Host和SNI后已可正常使用。

3）接入点可以直接使用我的自选IP域名`uicdn.cf`，每30分钟更新一次。

#### 交流群[https://t.me/iflugp](https://t.me/iflugp)

## v2ray-heroku
[![](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy?template=https://github.com/esonem/xryun.git)

### heroku上部署v2ray
- [x] 支持VMess和VLESS两种协议
- [x] 支持自定义websocket路径
- [x] 伪装首页（3D元素周期表）
- [x] HTML5测速
- [x] 使用v2ray最新版构建

请求`/`，返回3D元素周期表

![image](https://cdn.jsdelivr.net/gh/esonem/xryun/doc/1.png)

请求`/speedtest/`，html5-speedtest测速页面

![image](https://cdn.jsdelivr.net/gh/esonem/xryun/doc/2.png)

请求`/test/`，文件下载速度测试

![image](https://cdn.jsdelivr.net/gh/esonem/xryun/doc/3.png)

请求`/ray`（可配置）v2ray websocket路径


### 环境变量说明

|  名称 | 值  | 说明  |
| ------------ | ------------ | ------------ |
|  PROTOCOL |  vmess<br>vless（可选） |  协议：nginx+vmess+ws+tls或是nginx+vless+ws+tls |
|  UUID |  [uuid在线生成器](https://www.uuidgenerator.net "uuid在线生成器") | 用户主ID  |
|  WS_PATH | 默认为`/ray` |  路径，请勿使用`/speedtest`，`/`，`/test` 等已经被占用的请求路径 |

### 进阶
heorku可以绑卡（应用一直在线，不扣费），绑定域名，套cf，[uptimerobot](https://uptimerobot.com/) 定时访问防止休眠（只监控CF Workers反代地址好了，不然几个账户一起监控没几天就把时间耗完了）

### CloudFlare Workers反代代码（可分别用两个账号的应用程序名（`PROTOCOL`、`UUID`、`WS_PATH`保持一致），单双号天分别执行，那一个月就有550+550小时）
<details>
<summary>CloudFlare Workers单账户反代代码</summary>

```js
addEventListener(
    "fetch",event => {
        let url=new URL(event.request.url);
        url.hostname="appname.herokuapp.com";
        let request=new Request(url,event.request);
        event. respondWith(
            fetch(request)
        )
    }
)
```
</details>

### 客户端配置

```
  - name: "yourName"
    type: vmess
    server: yourName.workers.dev
    port: 443
    uuid: yourUuid
    alterId: 0
    cipher: auto
    udp: true
    tls: true
    #skip-cert-verify: true
    servername: yourName.workers.dev
    network: ws
    ws-path: /ray
```
