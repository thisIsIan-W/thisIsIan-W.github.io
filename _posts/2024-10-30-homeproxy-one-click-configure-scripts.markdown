---
layout:     post
title:      "homeproxy 一键配置脚本使用说明"
subtitle:   "homeproxy 配置教程"
date:       2024-10-30
author:     "Ian"
header-img: "img/post-bg-js-module.jpg"
tags:
    - 代理工具
    - homeproxy
    - 配置教程
---



一键快速订阅、生成 Immortalwrt/Openwrt (23.0x+) homeproxy 界面绝大多数常用配置。

仓库  --> [https://github.com/thisIsIan-W/homeproxy-autogen-configuration](https://github.com/thisIsIan-W/homeproxy-autogen-configuration)

本 wiki 下方已给出示例配置，可酌情参考。



<br/>

<br/>

## 脚本功能
* 节省大量重复配置所需的时间；
* 解决因为误操作等原因导致的 HP 无法正常启动、使用问题；
* 仅支持 `自定义路由模式`。

<br/>

<br/>

## 使用手册

### 操作步骤



通过使用 Secret Gist 或其它私有链接定制你的专属 [参考 rules.sh](https://gist.github.com/thisIsIan-W/3d4343c6e61e49f4c5ae6aa9115045bf) 配置，之后回到 ImmortalWrt/OpenWrt 系统：

```bash
# 准备环境
opkg update
opkg install bash jq curl

# 执行(可重复执行此代码)
# https://ghp.p3terx.com/ 为镜像加速链接。如果不可用，自行将其替换为可用 URL 即可
bash -c "$(curl -kfsSl https://ghp.p3terx.com/https://raw.githubusercontent.com/thisIsIan-W/homeproxy-autogen-configuration/refs/heads/main/generate_homeproxy_rules.sh)"
```

之后回到浏览器，打开无痕标签页重新登陆后台，刷新 homeproxy 界面：

1. 其它需要你自定义的配置：
   1. 路由设置(Routing Settings) --> 手动选择默认出站
   2. 路由节点(Routing Rules) --> 手动选择出站
   3. DNS服务器(DNS Servers) --> 更改剩余DNS服务器出站
2. 保存并应用 ---> 完毕!

<br/>

<br/>

---



以下为 `rules.sh` 文件内参数说明。

<br/>

### <a name="param-description">参数说明</a>

`注意：`

除特殊说明外，列表内部 '标签名' 不可重复！！！

标签名仅支持英文大小写、下划线_、数字的单一或组合形式。

<br/>

#### SUBSCRIPTION_URLS(可选)
机场或代理服务器订阅链接(可选)。

格式为： `"URL#标签名"`。

其中 '标签名' 支持中文，但需要确保脚本文件的编码为 `UTF-8`.

* 脚本会在运行时自动帮你添加、订阅、生成节点到 homeproxy 中
* 若不想使用此功能，可直接删除整个 `SUBSCRIPTION_URLS=(xxx)` 代码块

`提示：你提供的链接将仅用于调用 homeproxy 订阅功能快速生成节点信息，不会出现隐私安全问题，请放心使用！`

```bash
SUBSCRIPTION_URLS=(
  "https://abc.xyz/subscribe?token=123#proxy_server_01"
  "https://yhb.com/subscribe?token=uhj#proxy_server_02"
  # More...
)
```

<br/>

<br/>

#### RULESET_URLS

规则集列表。

格式为：`标签名|URL(s)`

生成顺序：从上到下

1. direct_out(直连) 和 reject_out(广告&隐私) 为保留标签名称不可更改
   * 若不想使用它们，删除整个 direct_out(xxx) 或(和) reject_out(xxx) 代码块
2. 标签名 及其内 URL(s) 可以随意添加、修改、删除、重排，`但同一条规则集url在整个 RULESET_URLS(xxx) 代码块中只允许出现一次`
3. URL(s) 支持远程URL & 本地绝对路径、`.srs` 和 `.json` 类型文件
4. 脚本会自动识别 链接/本地路径 中的文件名，并为你生成对应的规则集文件名



脚本会按照`标签名`从上到下的顺序：

1. 为 `路由节点(Routing Nodes)`、`路由规则(Routing Rules)`、`DNS规则(DNS Rules)` 功能生成所有以 标签名 命名的条目
2. 为 `路由规则(Routing Rules)`、`DNS规则(DNS Rules)` 中的 `规则集(Rule Set)` 选中所有 标签名 对应的规则集
3. 为 `规则集(Rule Set)` 功能生成所有规则集

<br/>

#### 错误写法
适用于 `RULESET_URLS` 以及下方的 `DNS_SERVERS`.

```txt
# 错误写法一(双引号不允许另起一行书写)：
RULESET_URLS=(
  "google|url
  "
)
# 错误写法二(不允许在url列表中使用注释符号 --> #)：
RULESET_URLS=(
  "google|url1
  #url2
  url3"
)
# 错误写法三(不允许在url列表中出现多余换行)：
RULESET_URLS=(
  "google|url1

  url2"
)
```

<br/>

<br/>


#### DNS_SERVERS
DNS服务器列表，在这里配置你想要使用的 ***DNS服务商***。

格式为：`标签名|URL(s)`

生成顺序：从上到下

1. 标签名 的顺序为 `DNS服务器(DNS Servers)` 中每个条目顺序，一个标签支持多条 URLs
2. DNS_SERVERS(xxx) 中的 标签名 及 URL(s) 可随意增删修改、调整顺序
3. 同一条 URL 可以在同一个标签内 或 多个标签内多次出现
4. 最后一条 `标签名` 里的 `第一条URL` 会被作为 `默认DNS服务器` 选中
   1. 请注意在 `路由设置 - 默认出站` 以及  `DNS 服务器 - 该服务器出站` 功能中勾选相同的出站以防止出现服务无法正常启动或使用的问题

5. URL(s) 可填写类型: UDP, TCP, DoT, DoH, and RCode

```txt
DNS_SERVERS=(
  # 会生成一条名称为 dns_server_google 的 DNS服务商
  "google|url"

  # 会生成3条DNS服务器条目: 
  #     dns_server_cloudflare_1
  #     dns_server_cloudflare_2
  #     dns_server_cloudflare_3
  # 且 url 会按照顺序分配给上述3个服务器
  "cloudflare|
  url1
  url2
  url3"

  # More...
)
```

<br/>

<br/>

### 示例配置

懒得看 [参数说明](#param-description) 的用户可直接参考下方配置。

```txt
#!/bin/bash

# 订阅链接
SUBSCRIPTION_URLS=(
  # 若标签名包含中文，需要确保当前文件的编码为UTF-8，否则界面乱码
  "https://airport01.subscription.url/subscribe?token=xxxx#机场01"
  "https://airport02.subscription.url/subscribe?token=yyyy#机场02"
)

#
# RULESET_URLS 中的标签名作为 DNS_SERVERS 中标签名的前缀(忽略 reject_out 和 direct_out 标签)
# 那么 DNS服务器 和 DNS规则 功能中相同前缀的出站会被自动勾选
# 两者中标签的顺序不需要保持一致
#
RULESET_URLS=(

  "reject_out|/etc/homeproxy/ruleset/adblockdns.srs"

  "HK_proxy_server_01|
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/google.srs
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/googlefcm.srs
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/google-play.srs
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/google-cn.srs
  https://raw.githubusercontent.com/KaringX/karing-ruleset/sing/geo/geosite/google-trust-services@cn.srs
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/google-gemini.srs
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geoip/google.srs"

  "SG_proxy_server_01|
  https://raw.githubusercontent.com/SagerNet/sing-geosite/refs/heads/rule-set/geosite-openai.srs
  https://raw.githubusercontent.com/SagerNet/sing-geosite/refs/heads/rule-set/geosite-bing.srs
  https://raw.githubusercontent.com/KaringX/karing-ruleset/sing/geo/geoip/ai.srs"

  "SG_proxy_server_02|
  https://raw.githubusercontent.com/SagerNet/sing-geosite/refs/heads/rule-set/geosite-discord.srs
  https://raw.githubusercontent.com/SagerNet/sing-geosite/refs/heads/rule-set/geosite-twitch.srs
  https://raw.githubusercontent.com/SagerNet/sing-geosite/refs/heads/rule-set/geosite-amazon.srs
  https://raw.githubusercontent.com/SagerNet/sing-geosite/refs/heads/rule-set/geosite-amazon@cn.srs
  https://raw.githubusercontent.com/SagerNet/sing-geosite/refs/heads/rule-set/geosite-amazontrust.srs"

  "US_proxy_server_02|
  https://raw.githubusercontent.com/SagerNet/sing-geosite/refs/heads/rule-set/geosite-twitter.srs
  https://raw.githubusercontent.com/SagerNet/sing-geosite/refs/heads/rule-set/geosite-x.srs
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geoip/twitter.srs
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/tiktok.srs"
  
  "US_IPV6_proxy_server_02|
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/telegram.srs
  https://raw.githubusercontent.com/DustinWin/ruleset_geodata/sing-box-ruleset/telegramip.srs"
  
  "direct_out|/etc/homeproxy/ruleset/MyDirect.json
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geoip/cn.srs
  https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geosite/cn.srs"
)

DNS_SERVERS=(
  "HK_proxy_server_01|https://1.1.1.1/dns-query"
  "SG_proxy_server_01|https://1.1.1.1/dns-query"
  "SG_proxy_server_01|https://1.1.1.1/dns-query"
  "US_proxy_server_02|https://1.1.1.1/dns-query"
  "US_IPV6_proxy_server_02|https://1.1.1.1/dns-query"
  
  # https://8.8.8.8/dns-query 会被作为默认DNS服务器
  "Default_DNS_Server|https://8.8.8.8/dns-query
  rcode://refused"
)
```
