# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.10.0] - 2026-11-17

### Added

- Self-built ASN database `geoip/asn.mmdb` (MaxMind `GeoLite2-ASN` format, ~10 MB), built from [iptoasn.com](https://iptoasn.com) BGP routing data: 1,013,901 networks with autonomous system numbers and organizations, IPv4 and IPv6. Verified lookups include Telegram (AS62041), Cloudflare (AS13335), Google (AS15169) and Amazon (AS16509); unrouted and private ranges have no record, matching the official GeoLite2-ASN behavior.

### Changed

- Both Loon templates (`loon.conf`, `loon-malaysia.conf`): `ipasn-url` now points at this repository's own `geoip/asn.mmdb` instead of the third-party P3TERX GeoLite2-ASN mirror, so the Loon templates depend only on files hosted in this repository — the same approach as `geoip/cn-my.mmdb` for country lookups. Rules unchanged (12 sets, 304 rules).

## [1.9.0] - 2026-11-16

### Added

- Loon profile templates `loon.conf` and `loon-malaysia.conf`: Loon ports of the two Surge templates, with identical policy groups and rule order. Surge `RULE-SET` lines are converted to Loon `[Remote Rule]` entries, reusing the same 12 rule sets (304 rules) unchanged. Loon-specific settings: `doh-server`, `ip-mode = v4-only`, `real-ip`, `geoip-url` pointing at the same `geoip/cn-my.mmdb`, and a separate `ipasn-url` (P3TERX GeoLite2-ASN mirror), because Loon matches `IP-ASN` rules against its own ASN database. The `[Proxy]` section carries Loon-syntax VLESS examples (REALITY + Vision, WebSocket + TLS, RAW + TLS + Vision) and a commented `tailscale` node example with `exit-node`. Verified working on Loon 3.5.4 (Build 1016). Rules unchanged (12 sets, 304 rules).

## [1.8.0] - 2026-11-16

### Added

- Self-built GeoIP database `geoip/cn-my.mmdb` (MaxMind `GeoLite2-Country` format, 379 KB), built from the Loyalsoldier/geoip CIDR lists: 9,640 China (CN) networks and 3,989 Malaysia (MY) networks, IPv4 and IPv6. Both profile templates now set `geoip-maxmind-url` to this file, so `GEOIP,CN` and `GEOIP,MY` resolve from one database hosted in this repository.
- `surge-malaysia.conf`: new `GEOIP,MY,DIRECT` rule directly above `GEOIP,CN`, so Malaysian destination IPs always go direct no matter what `FINAL` is set to. Rules unchanged (12 sets, 304 rules).

## [1.7.1] - 2026-11-14

### Changed

- Both profile templates (`surge.conf` and `surge-malaysia.conf`) now set `internet-test-url` to Huawei's connectivity check endpoint (`http://connectivitycheck.platform.hicloud.com/generate_204`) and `proxy-test-url` to Google's (`http://www.google.com/generate_204`), so Surge tests direct internet connectivity and proxy connectivity against separate endpoints. Rules unchanged (12 sets, 304 rules).

## [1.7.0] - 2026-11-13

### Added

- Profile Reload & DNS Flush panel (`modules/panel/Reload-DNS.sgmodule` + `reload-dns.js`): tap the panel's refresh button to flush the DNS cache and reload the current profile, via Surge's own HTTP API (`$httpAPI`, `POST /v1/dns/flush` then `POST /v1/profiles/reload`, no authentication required per the official manual). No auto-refresh interval, so it never reloads the profile on its own; on failure the panel shows the error. No setup needed.

## [1.6.0] - 2026-11-13

### Removed

- `rules/Telegram.list` is retired (6 domain rules: `telegram.org`, `telegram.me`, `t.me`, `telegra.ph`, `telesco.pe`, `telegram.dog`). Those domains are no longer matched to the `💬 Telegram` group and now fall through to `FINAL`. Telegram IP matching is unchanged and still uses `rules/Telegram-ASN.list` (5 `IP-ASN` rules). Rule sets: 13 -> 12, total rules: 310 -> 304.

## [1.5.1] - 2026-11-13

### Added

- `DOMAIN-SUFFIX,mimecast.com` and `DOMAIN-SUFFIX,duosecurity.com` to `rules/Microsoft.list`. (`login.microsoftonline.com` was intentionally not added: `DOMAIN-SUFFIX,microsoftonline.com` already covers it.) Total rules: 308 -> 310.

## [1.5.0] - 2026-11-13

### Removed

- `rules/Social.list` is retired (35 rules: X, Instagram, Facebook, Reddit, Discord, international TikTok, etc.). Those domains are no longer matched to the `🌍 Global` group and now fall through to `FINAL` instead.
- `rules/Netflix.list` is retired (8 rules: Netflix domains, video CDN, and the `fast.com` speed test). Those domains are no longer matched to the `📺 Streaming` group and now fall through to `FINAL` instead. Rule sets: 15 -> 13, total rules: 351 -> 308.

## [1.4.1] - 2026-11-13

### Fixed

- Subscription Info panel: the subscription URL is now passed to the script verbatim instead of being parsed as a `url=` query parameter, so subscription links containing `&` (e.g. `?token=xxx&flag=1`) are no longer truncated at the first `&` and fetched incomplete. The old `url=<link>` form is still accepted.

## [1.4.0] - 2026-11-13

### Added

- Information panels under `modules/panel/`: **IP Check** (`IP-Check.sgmodule` + `ip-check.js`, current outbound IP / location / ISP via ipinfo.io, no key required) and **Subscription Info** (`Sub-Info.sgmodule` + `sub-info.js`, traffic used / remaining and expiry date from the `subscription-userinfo` header). Both refresh hourly and follow the official Information Panel format (`[Panel]` + generic script). The subscription URL is entered in the module settings in Surge and stored on the device only — it is never committed to this repository.

## [1.3.0] - 2026-11-13

### Changed

- Telegram IP matching now uses autonomous systems instead of a fixed CIDR list: `rules/Telegram-IP.list` (11 IP ranges) is replaced by `rules/Telegram-ASN.list` with 5 `IP-ASN` rules (AS62041, AS62014, AS59930, AS44907, AS211157). Telegram's new IPs are covered automatically, with no CIDR maintenance. Domain rules in `rules/Telegram.list` are unchanged. Total rules: 356 -> 350.

## [1.2.0] - 2026-11-12

### Added

- Malaysia / abroad profile template (`surge-malaysia.conf`): routing is inverted for use outside mainland China — China services go through a China-return node, overseas services go direct, and `FINAL` is direct
- VLESS examples in `surge.conf` (REALITY + Vision, WebSocket + TLS, RAW + TLS + Vision), following the official Surge 6 manual; VLESS requires Surge iOS 6.0.0+ / Mac 6.12.0+
- VLESS and Malaysia / abroad sections in both READMEs

## [1.1.0] - 2026-11-11

### Added

- Bilingual README header with a full Chinese edition (`README.zh-CN.md`)
- Badges (GitHub stars, license, last commit, rule validation) and a one-click Surge import link
- `CONTRIBUTING.md` and GitHub issue templates for rule requests and misrouted domains
- Automated rule validation: `scripts/validate_rules.py` runs locally and in GitHub Actions on every push and pull request

## [1.0.0] - 2026-11-11

### Added

- Initial release
- Surge 6 profile template (`surge.conf`) with policy groups for Proxy, AI, Streaming, Telegram, Apple, Global, Domestic, and Final
- 15 self-maintained rule sets with 356 unique rules: Reject, AI, YouTube, Netflix, Streaming, Telegram, Telegram-IP, Social, Google, Microsoft, Developer, Global, Apple, China, Private
- Surge module template (`modules/template.sgmodule`) and a maintenance guide
