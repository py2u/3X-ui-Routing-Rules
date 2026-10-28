# 🚦 3x-ui / Xray 分流规则

<p align="center">
  <img src="assets/3x-ui-routing.svg" alt="3x-ui Xray routing icon" width="180">
</p>

<p align="center">
  <strong>🇨🇳 国内直连 · 🌍 海外代理 · 🚫 广告拦截 · 🏠 局域网直连</strong>
</p>

<p align="center">
  <code>direct</code> · <code>proxy</code> · <code>block</code>
</p>

## 📄 直接查看配置文件

👉 [打开 routing.json](https://github.com/py2u/3X-ui-Routing-Rules/blob/main/routing.json)

📦 [查看完整 GitHub 仓库](https://github.com/py2u/3X-ui-Routing-Rules)

### 🔗 可直接读取的 Raw URL

如果 3x-ui 或其他工具需要通过 URL 读取纯 JSON，请使用下面这个地址：

🔗 https://raw.githubusercontent.com/py2u/3X-ui-Routing-Rules/main/routing.json

👉 [点击测试 Raw 配置 URL](https://raw.githubusercontent.com/py2u/3X-ui-Routing-Rules/main/routing.json)

⚠️ `github.com/.../blob/main/...` 是网页预览地址；程序读取配置时应使用 `raw.githubusercontent.com/...` 地址。

这是一份面向个人使用的 Xray routing 模板，适用于通过 3x-ui 管理 Xray 服务端的场景。

> 🧩 **定位**：这是 routing 层配置，不是包含节点密码、UUID 和 Reality 私钥的完整生产配置。

## 🎯 规则目标

- 🏠 局域网和私有地址走 `direct`
- 🚫 广告域名走 `block`
- 🇨🇳 中国大陆域名和 IP 走 `direct`
- 🤖 ChatGPT、OpenAI、Claude、Gemini、Perplexity 走 `proxy`
- ▶️ Google、YouTube 走 `proxy`
- ✈️ Telegram、GitHub 走 `proxy`
- 🌍 其他未匹配流量走 `proxy`

规则按从上到下的顺序匹配，第一条匹配成功后停止继续判断。因此不要随意调整顺序，尤其不要把最后的兜底规则放到前面。

每条规则还设置了 `ruleTag`，例如 `🏠-private-direct`。它是规则名称，不是出站标签，不会改变分流结果；Xray 可以在日志中用它标识具体命中的规则。

## 🗺️ 流量路线图

```text
                         ┌─ 🏠 私有地址 ────────> 🟢 direct
                         │
客户端请求 ──> Xray 路由 ├─ 🚫 广告域名 ────────> 🔴 block
                         │
                         ├─ 🇨🇳 国内域名/IP ─────> 🟢 direct
                         │
                         ├─ 🤖 AI / ▶️ 视频 / ✈️ 社交 ─> 🔵 proxy
                         │
                         └─ 🌍 其他流量 ────────> 🔵 proxy
```

## 📊 规则优先级

| 顺序 | 图标 | 匹配对象 | 出站 | 目的 |
| ---: | :---: | --- | --- | --- |
| 1 | 🏠 | 私有 IP | `direct` | 保证局域网可访问 |
| 2 | 🚫 | 广告域名 | `block` | 减少广告和跟踪 |
| 3 | 🇨🇳 | 中国大陆域名 | `direct` | 降低国内访问延迟 |
| 4 | 🤖 | AI 服务 | `proxy` | 提高可用性 |
| 5 | ▶️ | Google / YouTube | `proxy` | 保证页面和视频资源完整 |
| 6 | ✈️ | Telegram / GitHub | `proxy` | 保证连接稳定 |
| 7 | 🇨🇳 | 中国大陆 IP | `direct` | 处理未收录国内域名 |
| 8 | 🌍 | 其他流量 | `proxy` | 海外流量兜底 |

## 📁 文件说明

`routing.json` 是可以复制到 3x-ui Xray 配置中的 routing 部分：

```json
{
  "routing": {
    "domainStrategy": "IPIfNonMatch",
    "rules": []
  }
}
```

如果你的 3x-ui 页面提供独立的 Routing Rules 编辑器，请把 `routing.json` 中的 `routing` 对象内容按页面字段录入，不要把整份文件当作完整的 Xray 主配置覆盖进去。

> 📝 JSON 本身不支持 `//` 注释，因此本仓库使用 Xray 原生支持的 `ruleTag` 来添加图标和规则名称。

## 🏷️ 必须确认的出站标签

当前模板使用以下标签：

| 标签 | 作用 | 常见 Xray 协议 |
| --- | --- | --- |
| 🟢 `direct` | 直接连接目标地址 | `freedom` |
| 🔵 `proxy` | 通过上游代理节点连接 | VLESS、VMess、Trojan、Shadowsocks、WireGuard 等 |
| 🔴 `block` | 丢弃连接 | `blackhole` |

如果你的实际标签不同，必须全局替换 `routing.json` 中的 `outboundTag` 值。例如：

```text
direct  -> freedom
proxy   -> proxy-out
block   -> blackhole
```

标签区分大小写，并且必须已经存在于 Xray 的 `outbounds` 中。

### ⚡ 快速替换标签

如果你的 3x-ui 使用下面这组标签：

```text
直连：freedom
代理：proxy-out
拦截：blackhole
```

则将 `routing.json` 中的标签替换为：

```text
direct  → freedom
proxy   → proxy-out
block   → blackhole
```

> ✅ 替换完成后，必须确认 `outbounds[].tag` 中确实存在这些名称。

## 🔐 `proxy` 出站说明

本仓库只保存路由规则，不保存任何节点凭据。`proxy` 出站需要你在自己的 3x-ui 中配置，例如上游 VLESS、VMess、Trojan 或 WireGuard 节点。

不要把以下内容提交到 GitHub：

- UUID
- Reality 私钥
- Trojan 密码
- Shadowsocks 密钥
- 订阅链接
- 3x-ui 面板密码
- 完整的生产环境 `config.json`

如果只是普通的单 VPS 节点，并没有配置上游代理出站，那么 `proxy` 标签没有实际目标，最后的兜底规则会失败。此时要么配置真正的 `proxy` 出站，要么把兜底流量改成 `direct`。

## 🧭 `domainStrategy`

```json
"domainStrategy": "IPIfNonMatch"
```

含义是：先按域名规则匹配；域名没有匹配成功时，再解析域名并尝试用 IP 规则匹配，例如 `geoip:cn`。

## 📥 导入步骤

1. 备份当前 3x-ui 的 Xray 配置。
2. 确认 `direct`、`proxy`、`block` 三个出站标签真实存在。
3. 打开 `routing.json`。
4. 将其中的 `routing` 部分合并到现有 Xray 配置。
5. 保留 3x-ui 自动生成的 API、统计和面板相关规则。
6. 保存后执行配置检查。
7. 重载或重启 Xray。
8. 分别测试国内网站、YouTube、ChatGPT、Telegram、GitHub 和局域网地址。

不要直接用这份文件覆盖完整 Xray 配置，因为它只包含 routing，不包含你的 inbounds、outbounds、DNS、policy 和传输层参数。

## ✅ 导入后测试清单

- [ ] 🏠 可以访问路由器、NAS 或其他局域网地址
- [ ] 🇨🇳 百度、淘宝、B 站等国内网站走 `direct`
- [ ] 🤖 ChatGPT 或其他 AI 服务走 `proxy`
- [ ] ▶️ YouTube 页面和视频都可以播放
- [ ] ✈️ Telegram 可以连接和收发消息
- [ ] 🐙 GitHub 网页、Release 和 Raw 文件可访问
- [ ] 🚫 广告域名被 `block` 出站拦截
- [ ] 🌍 未知海外网站可以通过兜底代理访问

查看日志时，可以重点搜索：

```text
outbound not found
unknown geosite
failed to resolve
connection refused
```

## 🩺 常见错误

### ❌ `outbound not found`

说明规则引用了不存在的出站标签。检查 `outboundTag` 与 `outbounds[].tag` 是否完全一致。

### ⚠️ `unknown geosite`

说明当前 Xray 核心或 geodata 文件不支持对应的 geosite 分类。可以先更新 Xray 核心和 geodata；如果暂时不能更新，删除 `geosite:category-ads-all` 或 `geosite:cn`，改成明确的域名规则。

### 🇨🇳 国内网站仍然走代理

检查：

1. `geosite:cn` 是否可用；
2. `geoip:cn` 数据是否存在；
3. `direct` 标签是否确实指向 `freedom`；
4. 客户端是否启用了自己的路由规则；
5. DNS 是否被客户端或服务器改写。

### ▶️ YouTube 页面能打开但视频不能播放

确认规则中包含：

```text
googlevideo.com
```

它通常负责 YouTube 的视频流量。

## 🔀 服务端分流和客户端分流的区别

如果你的目标是“国内网站使用家庭宽带直连，国外网站通过 VPS 代理”，主要规则应该配置在 Clash、sing-box、v2rayN 或 Shadowrocket 等客户端中。

3x-ui 服务端的 `direct` 表示由 VPS 直接访问目标网站，并不表示由你的家庭宽带直接访问。

本仓库的规则更适合：

- 服务端存在上游代理出站；
- 多级代理或中转；
- 服务端广告拦截；
- 不同目标走不同出口；
- 服务器统一控制访问策略。
