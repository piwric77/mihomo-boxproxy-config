# BoxProxy / TProxy 使用说明

## 当前状态

用户已实测 TProxy 模式可访问 Google。本版以 TProxy 为使用基线；APK 存在 eBPF 代码，但此前 eBPF 未完成可用性验证。如需测试 eBPF，按下方最新步骤切换；源 YAML 本身不负责加载内核程序。

## 导入和运行

1. 替换 config.yaml 的 proxy-providers → 机场订阅 → url，填自己的 Clash/Mihomo YAML 节点订阅。文件本身不包含真实节点。
2. 在 BoxProxy 导入并选中配置，核心选 Mihomo，网络模式选 TProxy，开启核心配置同步。代理范围选核心模式/core，开启 TCP、UDP 和 DNS 接管。
3. TProxy 端口 9898，Redirect 9797，DNS 1053。TUN 保持关闭。
4. 检查配置、更新订阅和规则、重启服务。已保存的策略选择会保留；若节点变更，应重新核对所选项。

## 节点选择

| 分组 | 用法 |
|---|---|
| 国外代理 | 全局默认自动选择，也可指定地区 |
| Google | 保留独立 Google/YouTube 分流及其 QUIC 兼容规则 |
| AI | 可直接选具体订阅节点，或选地区/国外代理 |
| Telegram | 可直接选具体订阅节点，独立于 AI；匹配域名及 Telegram IP |
| 美国、香港、日本、台湾、新加坡 | 默认本地区自动选择，也可手选 |
| 手动选择、其他节点 | 全部有效节点或未匹配上述地区的节点 |

AI 覆盖上游 category-ai-!cn 规则中的 ChatGPT、Claude、Gemini、Copilot、Perplexity 等服务。AI 规则先于 Google；Gemini 等专用 AI 域名走 AI，Google 账号等共享域名仍可能走 Google。国内服务维持国内分流，上游分类可能包含一些国际版中国厂商 AI 域名。

AI 和 Telegram 默认跟随国外代理，避免缺少某个地区时空组断网。若希望出口稳定，分别在这两个组内直接选一个具体节点；不要选自动地区组。节点是否支持某个 AI 服务取决于服务地区、节点出口信誉及账号条件，普通测速不能检测解锁。

分组顺序为国外代理、Google、AI、Telegram、去广告、地区组，隐藏自动组放到末尾。全部分组有图标；6 个自动选择组设置 hidden: true，继续工作。地区组内仍可选自动项。隐藏效果取决于面板是否支持，图标需联网加载。

## 延迟不稳

本版将健康检查/自动选择间隔从 300 秒调到 600 秒，切换容差从 50 ms 调到 150 ms，保留 lazy: true。减少周期测速和小幅抖动引起的切换；代价是周期评估不如原来频繁，故障恢复也可能变慢。没有修改超时来伪装更低延迟。

这些参数不会修复节点拥堵、移动网络抖动或上行被占满。建议固定一个节点，暂停下载/热点大流量，观察 5–10 分钟；再用同一节点对比 Wi-Fi 和移动网络。仅某个节点飙升时换节点；多个节点只在同一网络飙升时优先查本地网络。日志中曾有大量国内后台连接，但仅凭连接条数不能判定占用带宽。

面板显示的是到测试地址的 HTTP 延迟，不等于 AI 或 Telegram 的实际响应时间。不要持续手动对全部节点测速来观察抖动。

## 热点

在 APP 共享网络中添加实际热点接口，确保不在排除列表；可用手机 Root 终端 ip -br addr 核对热点网关所在接口。MAC 过滤先关闭，空 MAC 白名单可能跳过代理。保存后重新应用规则。热点实测需分别核对国内和国外请求。

手动 HTTP/SOCKS 入口为手机热点网关 IP:7890，无密码，只用于自己控制的网络。管理 API 监听 127.0.0.1:9090。YAML 关闭 IPv6 解析并不保证阻止热点 IPv6 绕行，须结合 APP 系统 IPv6 设置与实际测试。

## 验证与参考

本次已检查策略引用、6 个隐藏自动组、AI/Telegram 规则优先级和测速参数；未在手机验证新增服务、延迟改善或热点。规则集每日经国外代理更新，首次使用需确认下载成功。

- [Mihomo 自动选择参数](https://wiki.metacubex.one/config/proxy-groups/url-test/)
- [AI 域名规则](https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/meta/geo/geosite/category-ai-!cn.list)
- [Telegram 域名规则](https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/meta/geo/geosite/telegram.list)
- [Telegram IP 规则](https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/meta/geo/geoip/telegram.list)
- [MyClash 分组参考](https://github.com/AIsouler/MyClash)


## DNS 加固与去广告（最新）

所有配置内 DNS 上游改为 DoH。引导解析、节点域名及国内直连解析走国内加密 DNS；Google、AI、Telegram 等国外解析走相应代理组，其余默认国外解析也经代理。国内 DNS 是国内外分流的预期行为，不是所有 DNS 都从国外出口发出。

已去掉 system DNS 回退，使用 IP 形式的 DoH 地址避免为 DNS 服务器域名先发明文查询。局域网私有域名可能因此无法解析；请使用局域网 IP，或在明确自己的 LAN DNS 地址后仅为私有后缀增加定向解析。prefer-h3 关闭，使用 TCP DoH；respect-rules 为 false，路由由各 DoH URL 的 #代理组/#DIRECT 显式指定。

BoxProxy 内还必须开启 TCP/UDP 53 劫持，覆盖本机和共享网络。规则把进入核心的 53 端口交给内置 DNS，并阻止进入核心的 TCP/UDP 853。关闭 Android 私人 DNS，以及浏览器/应用“安全 DNS/自定义 DoH”，让系统请求统一进入 Mihomo。应用内 DoH 使用 HTTPS/443，不能靠 53 端口劫持保证拦住。

IPv6 暂不启用：若设备仍有 IPv6 直连，需在 APP 选择禁用系统 IPv6，或另行完整配置 IPv6 接管。“不进核心”不等于禁用 IPv6。热点客户端也要核对私人 DNS/DoH 和 IPv6。只有这些流量实际进入核心，YAML 的 DNS 规则才生效。

去广告默认 REJECT；切到 PASS 可关闭并继续后续分流。规则来源 MetaCubeX category-ads-all，每日经代理更新。它是域名级拦截，不能去除所有视频内嵌广告；误拦截时先切 PASS 验证，必要时按具体域名增加白名单。不把广告域名永久写成 DNS 0.0.0.0，确保开关能够控制路由拦截。

## BoxProxy eBPF 配套支持

同一 config.yaml 可作为 BoxProxy 的源配置；请在 APP 中选择 Mihomo + eBPF，开启核心配置同步、TCP/UDP 接管和 DNS 劫持，选择 core 范围，并添加实际热点接口。eBPF 运行时入站和路由由 APP 生成，文件不会自行加载 eBPF，也没有可通用添加的 ebpf: true。

切换后重启服务并检查 boxctl 运行日志是否成功应用规则，随后访问网站检查核心连接记录。若只有端口监听而没有流量，说明仍需检查接管，不应继续修改网站分流掩盖问题。若出现 BPF 加载/固定失败，需要设备内核和权限支持。已有证据仅确认 APK 包含相关代码、TProxy 在你的设备上能使用；eBPF 可用性仍未确认，本次没有宣称修复它。

失败时在 APP 切回 TProxy 并重新应用规则，可继续使用这份配置。请保留切换时的 boxctl 日志，才能定位 eBPF 未接管的原因。

本次完成分组排序、策略引用、隐藏组数量、广告规则优先级和 DNS 配置静态检查。尚未做手机 DNS 泄露、广告效果及 eBPF 实测，也未运行 Mihomo 核心语法检查。建议分别在手机/热点进行 DNS 测试，确认无意外运营商解析器及 IPv6 出口；单次网页测试也不能覆盖所有应用。

参考：[Mihomo DNS](https://wiki.metacubex.one/config/dns/)、[广告域名规则](https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/meta/geo/geosite/category-ads-all.list)、[PASS 语义](https://wiki.metacubex.one/config/proxies/built-in/)。
