# S.H.I.E.L.D.

神盾局 · Shadowrocket 分流与加密 DNS 配置，适用于 iPhone、iPad 和 Mac。

[下载主配置 S.H.I.E.L.D.conf](https://raw.githubusercontent.com/issachigga/S.H.I.E.L.D./main/S.H.I.E.L.D.conf) · [查看配置内容](S.H.I.E.L.D.conf)

`shadowrocket.conf` 保留为旧链接兼容副本，两份内容相同，不区分 Mac/iOS。旧副本的更新地址也指向主配置。

ClashMi（iOS / Android）及 Clash Meta for Android 用户请看 [Clash YAML 模板与使用教程](ANDROID.md)。需在本地加入自己的 Clash 格式节点/订阅，再导入使用；公开模板不含节点。ClashMi 还需检查客户端 DNS/规则覆写和 TUN 设置。

面向已有可用代理节点的 Shadowrocket 用户。配置使用 2.2.92 已有字段，建议升级至当前正式版。2026-11-01 核对 [App Store](https://apps.apple.com/us/app/shadowrocket/id932747118) 的最新版本为 **2.2.93**（2026-10-29 发布），包括 macOS 菜单栏、隧道路由、本地网络及稳定性修复；无需改用另一套 conf 格式。

## 导入与选择节点

### 一键导入（iPhone / iPad）

[点击导入 Shadowrocket 配置](https://lowertop.github.io/Shadowrocket-First/redirect.html?url=shadowrocket%3A%2F%2Fconfig%2Fadd%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fissachigga%2FS.H.I.E.L.D.%2Fmain%2FS.H.I.E.L.D.conf)

先安装 Shadowrocket，建议用 Safari 打开；允许跳转至 Shadowrocket，再按软件提示完成导入。若没有自动跳转，点击跳转页的“一键安装”按钮。导入后检查当前配置是否选中、全局路由是否为“配置”。先备份原配置。

说明：GitHub README 对自定义协议链接有限制，因此按钮经过第三方 LOWERTOP 的公开跳转页，页面可能变化或不可达；不附带节点或凭据，也没有为本仓库添加点击追踪。

不想经过第三方页面，可复制下列完整链接到 Safari 地址栏打开（需手机实测）：

```text
shadowrocket://config/add/https://raw.githubusercontent.com/issachigga/S.H.I.E.L.D./main/S.H.I.E.L.D.conf
```

### 手动导入备用地址

配置下载地址：

https://raw.githubusercontent.com/issachigga/S.H.I.E.L.D./main/S.H.I.E.L.D.conf

在 Shadowrocket「配置」中添加上述 URL，下载后选中配置；首页「全局路由」选择「配置」。先备份原配置。

**在首页手动选择节点。** 主组「🚀 节点选择」只保留 PROXY，国外服务组全部引用主组。不要在服务组中单独添加节点或 DIRECT。这样网页与裸 `#proxy` DNS 使用同一个首页代理选择，实际出口仍需实测确认。关闭“测试并选择最快服务器”等自动选择功能（若开启）。

节点和订阅由每个使用者自己添加，配置更新不会替你提供节点。首次导入需检查所有远程规则是否下载成功。

### Mac 端导入

Mac 与 iPhone/iPad 使用同一份主配置。这里没有网卡名、某台路由器的地址或设备专属进程规则，因此不需要维护两套 DNS/分流。系统的 VPN 权限、隧道及本地网络选项由各设备自己的客户端管理。

1. 从官方 App Store 安装/更新 Shadowrocket，在「配置」页面添加上方主配置 URL，或下载 `S.H.I.E.L.D.conf` 后通过本地文件导入。按钮名称以当前 Mac 版为准。
2. 使用这份配置，将全局路由设为「配置」，在首页选择自己的真实节点，启动连接并按 macOS 提示允许 VPN 配置。国外组统一跟随首页节点。
3. 2.2.93 提供菜单栏启动选项，可按个人习惯开启。若单个应用不跟随代理，检查它是否使用其他代理/VPN，以及小火箭的隧道设置；无需因此另下载“Mac 专用规则”。

两端分别检查 DNS 和 IPv6 出口；共用配置格式不代表两端已经完成运行测试。使用 iCloud 同步时，先检查同步后的当前配置和节点选择。

## 分流和 DNS

- Apple、国内媒体、小米、国内规则命中的网站直连；微软、Google、GitHub、AI 和其他国外服务走首页节点。
- 未命中的目标默认代理；国内综合规则使用 `ChinaMax_Domain.list` 中明确收录的域名，国内 IP 使用纯 IP 的 `ChinaIPs.list` 和 GeoIP CN，并保留 `no-resolve`。
- 不再引用混合的 `ChinaMax.list`，避免其中宽泛的 `.ms` 后缀、`stripe` 关键字及 User-Agent 规则将陌生域名判为直连。未收录的国内域名可能走代理；若需要例外，先核对再按完整域名/后缀添加。
- YouTube 规则位于 Google 规则之前；国外媒体同时引用 `GlobalMedia.list` 和配套 `GlobalMedia_Domain.list`。
- 主 DNS：Cloudflare DoH，经代理；备用：Google DoH，经代理。
- 直连域名 DNS：阿里、腾讯 DoH，不使用系统 DNS 覆盖。
- 节点入口域名：阿里、腾讯 DoH，独立于国外网站解析，避免代理入口解析循环。国内解析商仍能看到它负责的查询。
- 禁止系统 DNS 回退；直连解析失败允许经国外备用 DNS 处理，不保证国内 DNS 故障时国内网站仍可用。
- IPv6 开启，prefer-ipv6 关闭。IPv6-only 目标能否访问还取决于节点出口和网络。
- 节点不支持 UDP 时拒绝，不改成直连；通话、游戏可能因此失败。

DoH 加密传输，不保证解析商不记录查询。直连网站仍可能通过 IP、TLS 信息暴露目标。配置不提供绝对匿名或全设备 kill switch 保证。关闭 VPN 后系统恢复直连，不属于本配置覆盖范围。

内网 IP 可直接访问。当前 `private-ip-answer=false`，公共 DoH 不负责你家 NAS/PVE/OpenWrt 等设备的私有域名；使用私有域名时，在本地按需添加 Host/专用解析，不要把全局国外 DNS 改回系统 DNS。IPv6 本地 mDNS 组播 `ff02::fb/128` 已加入 TUN 旁路路由。

## QUIC 设置

v0.3 默认 `block-quic = all-proxy`：**只阻断走代理的 QUIC，DIRECT 直连保留 QUIC**。因此 YouTube、Instagram、X/Twitter 等代理流量会受到限制，其他走代理的服务也一样；范围不限于这三个 App。

它没有关闭所有 UDP，不会把 Hysteria2/TUIC 节点的传输协议改为 TCP。对支持回退的 HTTPS 请求，阻断 QUIC 后客户端通常会改用基于 TCP 的 HTTP/2 或 HTTP/1.1；不是把 UDP 数据转换成 TCP。不支持 TCP 回退的请求可能失败，也不保证阻断后一定更快。

三个可选值：

| 值 | 本开关的效果 |
| --- | --- |
| `all-proxy`（默认） | 代理 QUIC 阻断，DIRECT QUIC 保留 |
| `all` | 代理和 DIRECT 的 QUIC 都阻断 |
| `always-allow` | 关闭本开关的 QUIC 阻断 |

恢复代理 QUIC：在配置的纯文本编辑中，将 `block-quic = all-proxy` 改回 `block-quic = always-allow`。整份更新会恢复仓库的默认值。

更新后重新连接 VPN，再退出重开相关 App，避免旧连接继续沿用。用连接日志核对代理目标的协议；测试 DIRECT 网站时也要确认实际命中 DIRECT。上述范围仅描述这个开关，其他模块或上游路由器的拦截规则另行生效。

写法参考：[配置维护者的 QUIC 参数说明](https://github.com/LOWERTOP/Shadowrocket/blob/main/lazy.conf)。当前只完成文件检查，尚未在 iPhone/Mac 实测 QUIC 回退。

## 更新

### v0.3 · 2026-11-06

- 将 QUIC 设置改为 `all-proxy`：所有代理连接禁用 QUIC，DIRECT 直连保留。
- DNS、IPv6、分流规则和首页手选节点方案沿用 v0.2，主文件及旧链接副本同步更新。
- 补充三种开关值、恢复方法和回退验证说明。

### v0.2 · 2026-11-01

- 项目改名为 **S.H.I.E.L.D.**，主文件为 `S.H.I.E.L.D.conf`；旧文件保留兼容。
- Mac/iOS 共用一份配置，补充 Mac 导入步骤和 2.2.93 更新说明。
- 国内混合规则改为明确域名 + 纯 IP 列表，修正陌生域名被宽泛规则直连的问题。
- 补齐国外媒体域名集，将 YouTube 放到 Google 前面，并补充 IPv6 本地 mDNS 路由。

配置含 update-url，指向本仓库 main 分支的 `S.H.I.E.L.D.conf`。可在配置界面手动更新；自动更新需在软件设置中启用，iOS 后台调度不保证准时执行。仅刷新远程规则时，使用「使用配置 / 编译配置」；整份「更新配置」会覆盖本地编辑。

远程规则来自第三方仓库，经 jsDelivr CDN 获取，由 Shadowrocket 下载/缓存。检查刷新状态；CDN 可能延迟、不可达或上游变化，不承诺自动更新一定成功。若 raw GitHub 无法下载，先使用已有可用配置/节点，不删除原配置。

整份配置更新可能覆盖个人 DNS、规则、QUIC 及策略组修改。备份后再更新。请勿将本地节点订阅、CA 证书私钥、密码或带凭据日志提交至公开仓库。

## 测试状态与验证

v0.2 已检查配置结构、策略引用、远程规则可用性及域名匹配；v0.3 核对了 QUIC 参数和两份配置一致性，并确认 DNS、IPv6、策略组及分流规则与 v0.2 相同。**尚未在 iPhone/Mac 做完整 VPN 的 DNS 出口、IPv6 或故障回退实测，不保证零泄漏**。应用自带 DoH/DoQ、其他模块和特殊网络可能改变实际行为。

1. VPN 开启、配置模式下测试国内和国外域名，查看连接记录、命中策略及 DNS 出口。
2. 首页切换节点，重启相关应用/连接，确认新连接与 DNS 出口跟随。
3. VPN 保持开启、选定节点故障时，国外请求应失败，不应国内 DNS 回退或直连。
4. 分别验证 IPv4 和 IPv6；DNS 测试网页只可辅助判断解析商，不能证明路径。必要时结合路由器抓包。
5. 如失败，切回备份配置。分享日志前脱敏节点凭据、订阅、证书和私人访问记录。

## 上游规则

[blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)、[ACL4SSR](https://github.com/ACL4SSR/ACL4SSR)、[Aethersailor/Custom_OpenClash_Rules](https://github.com/Aethersailor/Custom_OpenClash_Rules)。规则通过链接引用，各自遵循上游许可；本项目不复制完整规则库。
