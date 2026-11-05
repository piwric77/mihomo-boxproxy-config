# Mihomo for BoxProxy

国内直连、国外分流，支持 IPv4/IPv6、地区自动选择、Google、AI、Telegram、FCM 与去广告。

## 配置下载

[查看并下载配置](https://github.com/piwric77/mihomo-boxproxy-config/blob/main/config.yaml) · [Raw 地址](https://raw.githubusercontent.com/piwric77/mihomo-boxproxy-config/main/config.yaml)

```text
https://raw.githubusercontent.com/piwric77/mihomo-boxproxy-config/main/config.yaml
```

这是公开模板。使用前请将 `在此填入订阅链接` 改为自己的 Clash/Mihomo 订阅链接；不要把私人订阅提交到公开仓库。

## 使用

1. 下载 `config.yaml`，填写订阅链接。
2. 在 BoxProxy 导入，选择 Mihomo + TProxy，开启核心配置同步、TCP/UDP、DNS 接管；如网络支持，再开启 IPv6。
3. 更新订阅和规则后，在“国外代理”或服务策略组中选择出口。

eBPF 的启用与热点流量接管由 BoxProxy 和设备内核决定，YAML 不单独开启它。热点共享时，请在 BoxProxy 选择实际热点接口；你的设备此前显示为 `wlan2`。

## 策略

- 美国、香港、日本、台湾、新加坡支持手动和隐藏的自动选择；检测间隔 180 秒、容差 150 ms。
- Google、AI、Telegram、FCM 均可独立选出口；FCM 默认跟随 Google。
- 去广告仅使用秋风官方“仅广告”规则，默认 `REJECT`。遇到误拦可在“去广告”改为 `PASS`。
- DNS 使用 DoH；国内域名直连国内 DNS，国外域名按代理策略解析。

## 延迟测试

订阅健康检查和自动选择使用 `https://www.gstatic.com/generate_204`。BoxProxy 首页延迟目标建议分别使用：

| 名称 | 地址 | 分流 |
|---|---|---|
| Baidu | `https://www.baidu.com/` | DIRECT |
| Cloudflare | `https://cp.cloudflare.com/` | 国外代理 |
| Google | `https://g.cn/generate_204` | Google |

## 参考

- [Mihomo 文档](https://wiki.metacubex.one/)
- [MetaCubeX 规则集](https://github.com/MetaCubeX/meta-rules-dat)
- [秋风广告规则](https://github.com/TG-Twilight/AWAvenue-Ads-Rule)
