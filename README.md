# Mihomo for BoxProxy

国内直连、国外分流，支持 IPv4/IPv6、地区自动选择、Google / AI / Telegram 独立策略及域名去广告。

## 配置下载链接

[打开配置文件并下载](https://github.com/piwric77/mihomo-boxproxy-config/blob/main/config.yaml) · [Raw 配置文件](https://github.com/piwric77/mihomo-boxproxy-config/raw/refs/heads/main/config.yaml)

可复制的配置地址：

```text
https://raw.githubusercontent.com/piwric77/mihomo-boxproxy-config/main/config.yaml
```

仓库已公开，以上链接可直接复制或免登录下载。这是配置模板地址，使用前需将「在此填入订阅链接」替换为自己的节点订阅；不要将含有私人订阅链接的配置提交到公开仓库。

## 使用

1. 下载 `config.yaml`，将 `在此填入订阅链接` 替换为 Clash/Mihomo 格式的节点订阅链接。
2. 在 BoxProxy 中导入，选择 Mihomo + TProxy，开启核心配置同步、IPv6 及 TCP/UDP、DNS 接管，重启服务。
3. 更新订阅和规则，在「国外代理」或各服务分组中选择节点。

IPv6 需要模块接管本机和热点的 IPv6 流量；运行配置中的全局 `ipv6` 和 `dns.ipv6` 应均为 `true`。eBPF 由 BoxProxy 管理，需设备内核及模块支持，YAML 本身不负责启用。

## 策略与共享

- 美国、香港、日本、台湾、新加坡分组支持自动或手动选择；自动组隐藏，订阅健康检查及全部自动组测速间隔 180 秒，切换容差 150 ms。
- Google、AI、Telegram 可独立选择出口；去广告选择 `REJECT` 开启、`PASS` 关闭。
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

## 参考

- [Mihomo 文档](https://wiki.metacubex.one/)
- [MetaCubeX 规则集](https://github.com/MetaCubeX/meta-rules-dat)
- [MyClash](https://github.com/AIsouler/MyClash)
- [Qure 图标](https://github.com/Koolson/Qure)
