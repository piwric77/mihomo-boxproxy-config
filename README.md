# Mihomo for BoxProxy

国内直连、国外分流，支持 IPv4/IPv6、地区自动选择、Google / AI / Telegram 独立策略。

## 配置下载链接

[打开配置文件并下载](https://github.com/piwric77/mihomo-boxproxy-config/blob/main/config.yaml) · [Raw 配置文件](https://github.com/piwric77/mihomo-boxproxy-config/raw/refs/heads/main/config.yaml)

可复制的配置地址：

```text
https://raw.githubusercontent.com/piwric77/mihomo-boxproxy-config/main/config.yaml
```

仓库已公开，以上链接可直接复制或免登录下载。这是配置模板地址，使用前需将「在此填入订阅链接」替换为自己的节点订阅；不要将含有私人订阅链接的配置提交到公开仓库。

## 本地订阅

除“机场订阅”外，配置还会载入 ./proxy_provider/local_subscription.yaml。该文件使用 Mihomo 的代理提供者格式，内容必须以 proxies: 开头；远程和本地节点会同时出现在地区、手动和自动选择组中。

仓库提供 [本地订阅模板](https://github.com/piwric77/mihomo-boxproxy-config/blob/main/local_subscription.yaml)。将模板复制到 Mihomo 配置目录的 proxy_provider/local_subscription.yaml，填写节点后重载配置即可。若客户端不允许写入这个路径，请使用该客户端的本地配置导入功能，而不是修改远程订阅。

## Bettbox

Bettbox 使用 Mihomo 内核，可直接导入本仓库的 config.yaml。在 Bettbox 中选择 Mihomo，导入配置后在应用内开启 TUN；不要启用会覆盖配置 DNS 的额外 DNS 覆写。需要本地节点时，将 local_subscription.yaml 放到 Bettbox 当前配置的 proxy_provider 目录，再重载配置。

热点共享时，之前设备截图显示接口为 wlan2，请在 BoxProxy 的“共享网络接口”选择它。Bettbox 的热点或网络共享接口由系统平台决定，配置本身不指定接口。

## 使用

1. 下载 `config.yaml`，将 `在此填入订阅链接` 替换为 Clash/Mihomo 格式的节点订阅链接。
2. 在 BoxProxy 中导入，选择 Mihomo + TProxy，开启核心配置同步、IPv6 及 TCP/UDP、DNS 接管，重启服务。
3. 更新订阅和规则，在「国外代理」或各服务分组中选择节点。

IPv6 需要模块接管本机和热点的 IPv6 流量；运行配置中的全局 `ipv6` 和 `dns.ipv6` 应均为 `true`。eBPF 由 BoxProxy 管理，需设备内核及模块支持，YAML 本身不负责启用。

## 策略与共享

- 美国、香港、日本、台湾、新加坡分组支持自动或手动选择；自动组隐藏，订阅健康检查及全部自动组测速间隔 180 秒，切换容差 150 ms。
- Google、AI、Telegram 可独立选择出口。
- 热点共享需在 BoxProxy 添加实际热点接口，并接管其 DNS 和 IPv6。
- 端口：混合代理 `7890`、Redirect `9797`、TProxy `9898`、DNS `1053`；管理接口仅监听 `127.0.0.1:9090`。

DNS 使用 DoH，国内查询直连国内上游，国外查询经相应代理。应用自带 DoH、私人 DNS 及未被模块接管的流量需另行配置。混合代理无密码，仅用于可信网络。

## 延迟设置

订阅健康检查和代理组统一使用 `https://g.cn/generate_204`；订阅节点连接采用 `ipv4-prefer`，双栈支持仍保留。若节点的 IPv6 路径更快，可移除 `proxy-providers` 下的 `override.ip-version`。

BoxProxy 首页「延迟目标」需要在 App 内单独保存，建议填写：

| 名称 | 地址 | 分流 |
|---|---|---|
| Baidu | `https://www.baidu.com/` | DIRECT |
| Cloudflare | `https://cp.cloudflare.com/` | 国外代理 |
| Google | `https://g.cn/generate_204` | Google |

首页测试与节点健康检查是不同入口。Cloudflare 和 Google 使用轻量检测地址；Baidu 保留网站连通性对照。对比配置时请固定同一节点、同一目标和网络，连续测数次观察中位数。修改目标后的数值不能与原网站首页直接比较。自动切换容差不会平滑首页显示的延迟。

## 去广告

去广告组保留 REJECT、REJECT-DROP 和 PASS 三种模式；默认 REJECT。改成 REJECT-DROP 可静默丢弃，改成 PASS 可临时关闭全部广告拦截，用于排查误拦。

仅使用秋风广告规则（TG-Twilight）官方“仅广告”MRS，每日更新。此版本不包含隐私统计及“不受欢迎”分类，减少对 FCM 推送、应用更新和正常服务的干扰。

## FCM 推送

FCM 策略默认跟随 Google，可单独选择固定节点或 DIRECT；推送连接及注册域名优先于通用 Google 规则匹配。固定节点有助于减少出口切换引起的重连，直连是否可用取决于网络。

BoxProxy 的接管范围需包含 Google Play 服务，允许 TCP 5228–5230 和 HTTPS 443；配置按域名分流，不将这些端口的所有应用流量都归入 FCM。系统后台限制、节点断线和模块是否接管也会影响推送，策略组不能保证通知即时送达。

参考：[Firebase 网络要求](https://firebase.google.com/docs/cloud-messaging/network-configuration)。

## 参考

- [Mihomo 文档](https://wiki.metacubex.one/)
- [MetaCubeX 规则集](https://github.com/MetaCubeX/meta-rules-dat)
- [MyClash](https://github.com/AIsouler/MyClash)
- [Qure 图标](https://github.com/Koolson/Qure)
