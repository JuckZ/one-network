# One Network

## TODO

- [ ] Sing-box-subscribe github actions支持
  - [ ] providers.json通过外部接口获取或者写成secrets放在github
  - [ ] 自定义templates文件
    - [ ] 使用自己的rules [sing-box-geosite](https://github.com/JuckZ/sing-box-geosite)
- [ ] sing-box tun模式 透明网关
  - [ ] https://sing-box.sagernet.org/configuration/inbound/tun/
- [ ] Home network
  - [ ] hiddify Connected timeout
- [ ] 独角兽 route.rules整合
- [ ] [WARP-Clash-API](https://github.com/vvbbnn00/WARP-Clash-API) to github action, 使用docker的方式运行
  - [ ] 数据库文件保存到外部数据库
  - [ ] [将 Cloudflare 的 Warp 节点信息直接提取出来加到 sing-box 出站配置中去](https://github.com/chika0801/sing-box-examples/blob/main/wireguard.md)

## warp-clash-api

hiddify 的 warp 功能

测试warp `https://cloudflare.com/cdn-cgi/trace`

```sh
docker-compose up -d
curl -o proxy.json "http://127.0.0.1:21001/api/sing-box?best=false&randomName=true&proxyFormat=with_groups&ipv6=false&key=123456"
# params
# proxyFormat: only_proxies with_groups full
# randomName: true false
# ipv6: true false
# best: true false
# api: account wireguard loon sing-box surge shadowrocket clash
```

## Sing box

### 透明网关

在网关上运行 sing-box 之后，其他机器只需要将网关指向这台机器，便可以无痛开启魔法智能分流了。

注意： 其他机器的 DNS 必须是公网 DNS，不能使用内网 DNS！你的 DNS 可以指向任意的公网 DNS，反正只要是公网就行，比如：114.114.114.114，因为 sing-box 会劫持局域网中的所有 DNS 请求。

当然，如果你不想让 sing-box 劫持局域网中的所有 DNS 请求，可以使用如下的方案：

首先在入站配置中添加一个监听端口：

```yml
{
  "inbounds": [
    {
      "type": "direct",
      "tag": "dns-in",
      "listen": "0.0.0.0",
      "listen_port": 53
    }
  ]
}
```

然后在路由规则中将 DNS 的规则改成如下的配置：

```yml
{
  "route": {
    "rules": [
      {
        "inbound": "dns-in",
        "outbound": "dns-out"
      }
    ]
  }
}
```

这样就保证了只有从 53 端口进来的流量才会进入 DNS 解析。

重启生效后，将其他机器的网关和 DNS 均指向这台机器就可以了。

如果你使用的是 DHCP，只需要在 DHCP 服务器中将 DHCP 分配的网关和 DNS 改成 sing-box 所在的机器 IP 即可。


## 参考

[sing-box-examples](https://github.com/chika0801/sing-box-examples/tree/main)
[sing-box 基础教程：sing-box 的配置方法和使用教程 · 云原生实验室](https://icloudnative.io/posts/sing-box-tutorial)
