# Mihomo for BoxProxy

国内直连、国外分流，支持 IPv4/IPv6、地区自动选择、Google / AI / Telegram 独立策略及域名去广告。

## 使用

1. 下载 `config.yaml`，将 `在此填入订阅链接` 替换为 Clash/Mihomo 格式的节点订阅链接。
2. 在 BoxProxy 中导入，选择 Mihomo + TProxy，开启核心配置同步、IPv6 及 TCP/UDP、DNS 接管，重启服务。
3. 更新订阅和规则，在「国外代理」或各服务分组中选择节点。

IPv6 需要模块接管本机和热点的 IPv6 流量；运行配置中的全局 `ipv6` 和 `dns.ipv6` 应均为 `true`。eBPF 由 BoxProxy 管理，需设备内核及模块支持，YAML 本身不负责启用。

## 策略与共享

- 美国、香港、日本、台湾、新加坡分组支持自动或手动选择；自动组隐藏，测速间隔 600 秒，切换容差 150 ms。
- Google、AI、Telegram 可独立选择出口；去广告选择 `REJECT` 开启、`PASS` 关闭。
- 热点共享需在 BoxProxy 添加实际热点接口，并接管其 DNS 和 IPv6。
- 端口：混合代理 `7890`、Redirect `9797`、TProxy `9898`、DNS `1053`；管理接口仅监听 `127.0.0.1:9090`。

DNS 使用 DoH，国内查询直连国内上游，国外查询经相应代理。应用自带 DoH、私人 DNS 及未被模块接管的流量需另行配置。混合代理无密码，仅用于可信网络。

## 参考

- [Mihomo 文档](https://wiki.metacubex.one/)
- [MetaCubeX 规则集](https://github.com/MetaCubeX/meta-rules-dat)
- [MyClash](https://github.com/AIsouler/MyClash)
- [Qure 图标](https://github.com/Koolson/Qure)
