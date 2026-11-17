# Surge6-Rules

[![GitHub stars](https://img.shields.io/github/stars/kkchan69/Surge6-Rules?style=social)](https://github.com/kkchan69/Surge6-Rules/stargazers)
[![License: MIT](https://img.shields.io/github/license/kkchan69/Surge6-Rules)](LICENSE)
[![Last commit](https://img.shields.io/github/last-commit/kkchan69/Surge6-Rules)](https://github.com/kkchan69/Surge6-Rules/commits/main)
[![Validate rules](https://github.com/kkchan69/Surge6-Rules/actions/workflows/validate-rules.yml/badge.svg)](https://github.com/kkchan69/Surge6-Rules/actions/workflows/validate-rules.yml)

[English](README.md) | **简体中文**

一套面向 **Surge 6（iOS）** 的分流仓库：自维护规则集 + 可直接套用的配置模板 + 模块模板。

目标很简单：国内直连、海外代理、广告拦截，AI / 流媒体单独分组，规则顺序清晰，后续好维护。

> 本仓库只包含规则和配置模板，**不包含、不提供任何代理节点或机场订阅**。节点请填你自己的。

> 如果这个项目对你有帮助，欢迎点一个 ⭐，能让更多 Surge 用户找到它，也是维护规则最好的动力。发现某个域名走错线路？欢迎提 [Issue](https://github.com/kkchan69/Surge6-Rules/issues/new/choose)，或看 [CONTRIBUTING.md](CONTRIBUTING.md)。

## VLESS

Surge 6 已原生支持 VLESS（iOS 6.0.0+ / Mac 6.12.0+），包括 REALITY 和 XTLS Vision，见[官方 VLESS 文档](https://manual.nssurge.com/policies/vless.html)。`surge.conf` 的 `[Proxy]` 段里有注释版 VLESS 示例——REALITY + Vision、WebSocket + TLS、RAW + TLS + Vision，只有占位符，请填自己的服务器、UUID、SNI 和 REALITY 公钥。XHTTP、HTTPUpgrade、mKCP 和 VLESS Encryption 不支持。

## 马来西亚 / 海外版

如果你在**中国境外**使用 Surge（例如在马来西亚），请用 `surge-malaysia.conf` 而不是 `surge.conf`。分流方向是反过来的：国内服务走回国节点（服务器必须在中国境内，海外节点解不了国内 App 的地区限制），海外服务直连，`FINAL` 也是直连。一键导入：

[surge:///install-config?url=https%3A%2F%2Fraw.githubusercontent.com%2Fkkchan69%2FSurge6-Rules%2Fmain%2Fsurge-malaysia.conf](surge:///install-config?url=https%3A%2F%2Fraw.githubusercontent.com%2Fkkchan69%2FSurge6-Rules%2Fmain%2Fsurge-malaysia.conf)

## 快速开始

### 一键导入（iOS，已安装 Surge）

点这个链接即可导入配置模板：

[surge:///install-config?url=https%3A%2F%2Fraw.githubusercontent.com%2Fkkchan69%2FSurge6-Rules%2Fmain%2Fsurge.conf](surge:///install-config?url=https%3A%2F%2Fraw.githubusercontent.com%2Fkkchan69%2FSurge6-Rules%2Fmain%2Fsurge.conf)

导入后记得先编辑配置：在 `[Proxy]` 填入你自己的节点，并把 `[Proxy Group]` 各组的成员改成你的节点名。模板默认只有 `DIRECT` 兜底，不改也能导入，但代理组会走直连。

### 方式一：整套配置

Surge App → 配置 → 从 URL 下载配置，粘贴：

```
https://raw.githubusercontent.com/kkchan69/Surge6-Rules/main/surge.conf
```

> 注意：用远程链接时，GitHub 上的版本才是生效版本。需要改节点段时，更推荐下载到本地改好后导入本地文件，或 fork 本仓库改成你自己的地址。

### 方式二：只引用规则集

在你现有配置的 `[Rule]` 段按顺序加入（域名集在前、IP 集在后，完整顺序以 `surge.conf` 为准）：

```ini
RULE-SET,https://raw.githubusercontent.com/kkchan69/Surge6-Rules/main/rules/Reject.list,REJECT
RULE-SET,https://raw.githubusercontent.com/kkchan69/Surge6-Rules/main/rules/AI.list,Proxy
RULE-SET,https://raw.githubusercontent.com/kkchan69/Surge6-Rules/main/rules/China.list,DIRECT
GEOIP,CN,DIRECT
FINAL,Proxy,dns-failed
```

## 规则集清单

| 规则集 | 用途 | 模板中策略 |
|---|---|---|
| `rules/Reject.list` | 广告与追踪拦截（起步版） | `REJECT` |
| `rules/AI.list` | OpenAI / ChatGPT、Claude、Gemini 入口、Perplexity、xAI 等 | `🤖 AI` |
| `rules/YouTube.list` | YouTube 与视频 CDN | `📺 Streaming` |
| `rules/Streaming.list` | Disney+、Max、Hulu、Twitch、Spotify 等 | `📺 Streaming` |
| `rules/Telegram-ASN.list` | Telegram 自治系统 AS62041、AS62014、AS59930、AS44907、AS211157（IP 集，放域名集之后） | `💬 Telegram` |
| `rules/Google.list` | Google 基础服务（不含 YouTube） | `🌍 Global` |
| `rules/Microsoft.list` | Microsoft / Office / OneDrive / LinkedIn 等 | `🌍 Global` |
| `rules/Developer.list` | GitHub、GitLab、npm、Docker 等 | `🌍 Global` |
| `rules/Global.list` | 其他海外常用服务精选 | `🌍 Global` |
| `rules/Apple.list` | Apple / iCloud / App Store 等 | `🍎 Apple`（默认直连） |
| `rules/China.list` | 国内常用服务精选（银行、支付、视频、出行、国内 AI 等） | `🏠 Domestic` |
| `rules/Private.list` | 局域网与私有 IP 段 | `DIRECT` |

订阅地址规律：`https://raw.githubusercontent.com/kkchan69/Surge6-Rules/main/rules/<文件名>`

## 规则顺序（重要）

Surge 从上到下匹配，**首个命中生效**。本仓库遵循：

1. `Reject` 拦截优先
2. 域名规则集：AI → 流媒体 → Google / 微软 / 开发者 → Apple → 国内
3. IP 规则集：Telegram-ASN、Private、内置 `LAN`——必须在域名集之后，避免不必要的 DNS 解析
4. 国内兜底：`DOMAIN-SUFFIX,cn` → `GEOIP,CN`
5. `FINAL` 最后兜底

## GeoIP 数据库

两个配置模板的 `geoip-maxmind-url` 都指向本仓库自建的 `geoip/cn-my.mmdb`（MaxMind `GeoLite2-Country` 格式，379 KB），数据源为 [Loyalsoldier/geoip](https://github.com/Loyalsoldier/geoip) 的 CIDR 清单：中国（CN）9,640 个网段、马来西亚（MY）3,989 个网段，含 IPv4 与 IPv6。一个文件同时支撑 `GEOIP,CN`（两个模板）和 `GEOIP,MY`（马来西亚模板，直连），CN/MY 以外的 IP 无记录，与纯 CN 的 chnroutes 库行为一致。

## Loon 版

用 [Loon](https://nsloon.app/) 而不是 Surge？`loon.conf` 和 `loon-malaysia.conf` 是两个 Surge 模板的 Loon 移植版——策略组、规则顺序完全一致，12 个规则集以远程规则方式引用同一批文件，两个 App 可以保持同步。在 Loon 里通过“配置 → + → 从 URL 下载配置”导入：

```
https://raw.githubusercontent.com/kkchan69/Surge6-Rules/main/loon.conf
https://raw.githubusercontent.com/kkchan69/Surge6-Rules/main/loon-malaysia.conf
```

与 Surge 模板的区别：GeoIP 库用 `geoip-url` 设置（仍是同一个 `geoip/cn-my.mmdb`），`IP-ASN` 匹配用单独的 ASN 库（`ipasn-url`，指向本仓库自建的 `geoip/asn.mmdb`，GeoLite2-ASN 格式，数据源为 BGP 路由表）。`[Proxy]` 示例是 Loon 语法，含注释版的 `tailscale` 节点示例（可做出口节点链）。已在 Loon 3.5.4 (1016) 实测可用。

## 面板

`modules/panel/` 里有三个信息面板，在 Surge 的“设置 → 模块”里安装对应的 `.sgmodule` 链接即可：

| 面板 | 显示内容 |
|---|---|
| IP 查询（`panel/IP-Check.sgmodule`） | 当前出口 IP、归属地、运营商 |
| 订阅流量（`panel/Sub-Info.sgmodule`） | 订阅已用 / 剩余流量、到期日 |
| 重载 DNS（`panel/Reload-DNS.sgmodule`） | 点刷新后清 DNS 缓存并重载当前配置 |

订阅面板需要在安装后在模块设置里填你自己的订阅链接，只存在你的设备上，绝不会写进仓库。详见 [`modules/README.md`](modules/README.md)。

## 目录结构

```
Surge6-Rules/
├── surge.conf          # Surge 6 配置模板（在中国境内使用，填入自己的节点后使用）
├── surge-malaysia.conf # Surge 6 配置模板（在中国境外使用，例如马来西亚，填入自己的节点后使用）
├── loon.conf           # Loon 配置模板（在中国境内使用，策略组与规则同 surge.conf）
├── loon-malaysia.conf  # Loon 配置模板（在中国境外使用，例如马来西亚）
├── rules/              # 12 个自维护 RULE-SET (.list，无策略列)
├── geoip/              # 自建数据库：cn-my.mmdb（CN+MY 国家）+ asn.mmdb（ASN，供 Loon ipasn-url）
├── modules/            # .sgmodule 模块模板、说明与面板模块
├── scripts/            # 规则校验脚本（GitHub Actions 同步运行）
├── docs/               # 维护说明
├── LICENSE
└── README.md
```

## 更新日志

见 [CHANGELOG.md](CHANGELOG.md)，当前版本 **v1.10.0**。

## 免责声明

本仓库仅供学习与个人网络管理使用，规则内容来自公开域名信息整理。请遵守当地法律法规和 Surge 的使用条款。

## License

MIT
