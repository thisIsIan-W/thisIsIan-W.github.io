---
layout:     post
title:      "Homeproxy 一键配置脚本使用说明"
subtitle:   "Homeproxy 配置教程"
date:       2024-10-30
author:     "Ian"
header-img: "img/post-bg-js-module.jpg"
tags:
    - 代理工具
    - Homeproxy
    - 教程
---



一键快速订阅、生成 Immortalwrt/Openwrt (23.0x+) Homeproxy 界面绝大多数常用配置。

仓库地址：[https://github.com/thisIsIan-W/homeproxy-autogen-configuration](https://github.com/thisIsIan-W/homeproxy-autogen-configuration)

<br/>

<br/>

<br/>

## 脚本功能
* 一键快速订阅你的所有机场或代理服务器节点
* 一键生成 出站规则、出站规则列表、DNS服务器、DNS规则列表、默认出站配置、自定义节点、克拉斯 API配置 以及 规则详情 等
* 支持 `3种` 方式定制上述规则配置
* 仅支持 `自定义路由模式`

<br/>

<br/>

## 使用脚本前
* 需要先从 Immortalwrt/Openwrt 应用市场安装最新版本的 Homeproxy
* 请先更新本地 sing-box 版本至最新版(可选，稳定/beta/alpha版本都可，1.10.0-Alpha25 之下不支持 Adguard Home 规则)
  * 下载内核后上传到设备的 `/usr/bin` 目录下覆盖原文件即可 (注意备份及权限)
* 安装或更新 HP 后，如出现界面异常等问题，手动清除浏览器缓存，或使用新的无痕标签页重新打开 HP 界面

<br/>

<br/>


## 使用手册

推荐使用 VSCode 等编辑器更改配置内容。

1. 从你的系统应用市场安装 Homeproxy;
2. 点击本仓库右侧绿色 `<> Code` 按钮并下载zip包到你的设备上并自定义 `rules_*.sh(下方三个文件任选其一)` 文件中的配置(下方提供详细说明);
   1. 按照规则集分流 ---> [rules_based_on_rulesets.sh](https://github.com/thisIsIan-W/homeproxy-autogen-configuration/blob/main/rules_based_on_rulesets.sh)
   2. 按照节点分流 ---> [rules_based_on_nodes.sh](https://github.com/thisIsIan-W/homeproxy-autogen-configuration/blob/main/rules_based_on_nodes.sh)
   3. 按照代理服务器分流 ---> [rules_based_on_proxy_servers.sh](https://github.com/thisIsIan-W/homeproxy-autogen-configuration/blob/main/rules_based_on_proxy_servers.sh)

3. 把2个脚本上传到你的设备上，目录随意，并赋予其执行权限，最后执行指定脚本;

```bash
# 首先需要确保当前账户为 root，类似于：(请自行搜索解决)
root@ImmortalWrt:~#

# 安装 bash(已安装则跳过此步)
opkg update
opkg install bash

# 需要确保2个脚本在一个目录下
# 举例，将2个脚本上传至 ImmortalWRT 或 OpenWRT 系统的 /tmp 目录下之后(可使用XShell/MobaXterm/PuTTY等客户端上传)：
cd /tmp

# 修改2个脚本的权限
chmod +x generate_homeproxy_rules.sh
chmod +x rules.sh

# 执行脚本一键生成配置
bash generate_homeproxy_rules.sh
```



完成上述步骤后回到浏览器，刷新 homeproxy 界面 (推荐使用 `无痕标签页` 重新打开以避免因缓存导致的各种奇怪问题);

1. 剩余需要自定义的配置：
   1. 路由设置(Routing Settings) --> 手动选择默认出站
   2. 路由节点(Routing Rules) --> 手动选择出站
   3. DNS服务器(DNS Servers) --> 更改剩余DNS服务器出站
2. 保存并应用

<br/>

<br/>

### 自用配置

懒得看下方 [参数说明](#param-description) 的用户可直接参考下方配置。

以下配置参考了 [rules_based_on_nodes.sh](https://github.com/thisIsIan-W/homeproxy-autogen-configuration/blob/main/rules_based_on_nodes.sh) 文件 (作者主路由配置)。

```txt
#!/bin/bash

SUBSCRIPTION_URLS=(
  "https://airport01.subscription.url/subscribe?token=xxxx#机场01"
  "https://airport02.subscription.url/subscribe?token=yyyy#机场02"
)

#
# RULESET_URLS 中的标签名作为 DNS_SERVERS 中标签名的前缀(忽略 reject_out 和 direct_out 标签)
# 那么 DNS服务器 和 DNS规则 功能中相同前缀的出站会被自动勾选
# 两者中标签的顺序不需要保持一致
# 
# 更详细的解释内容，请参阅 rules_*.sh 文件！
#
RULESET_URLS=(

  "reject_out|/etc/homeproxy/ruleset/adblockdns.srs"

  "direct_out|/etc/homeproxy/ruleset/MyDirect.json
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-microsoft@cn.srs
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-azure@cn.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/apple-cn.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geoip/cn.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/cn.srs"

  "Airport01_HK|/etc/homeproxy/ruleset/MyProxy.json
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/google.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/googlefcm.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/google-play.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/google-cn.srs
  https://github.com/KaringX/karing-ruleset/raw/sing/geo/geosite/google-trust-services@cn.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/google-gemini.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geoip/google.srs"

  "Airport02_US|/etc/homeproxy/ruleset/MyAI.json
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-twitter.srs
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-x.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geoip/twitter.srs
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-openai.srs
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-bing.srs
  https://github.com/KaringX/karing-ruleset/raw/sing/geo/geoip/ai.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/tiktok.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/telegram.srs
  https://github.com/DustinWin/ruleset_geodata/raw/sing-box-ruleset/telegramip.srs
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-discord.srs
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-twitch.srs
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-amazon.srs
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-amazon@cn.srs
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-amazontrust.srs"
)

DNS_SERVERS=(
  "Airport01_HK_google|https://8.8.8.8/dns-query"
  "Airport02_US_cf|https://1.1.1.1/dns-query"
  "Aliyun|223.5.5.5"
  
  "Default_DNS_Server|https://8.8.8.8/dns-query
  rcode://refused"
)
```

<br/>

以下配置参考了 [rules_based_on_rulesets.sh](https://github.com/thisIsIan-W/homeproxy-autogen-configuration/blob/main/rules_based_on_rulesets.sh) 文件。

```txt
#!/bin/bash

SUBSCRIPTION_URLS=(
  "https://airport01.subscription.url/subscribe?token=xxxx#机场01"
  "https://airport02.subscription.url/subscribe?token=yyyy#机场02"
)

#
# RULESET_URLS 中的标签名作为 DNS_SERVERS 中标签名的前缀(忽略 reject_out 和 direct_out 标签)
# 那么 DNS服务器 和 DNS规则 功能中相同前缀的出站会被自动勾选
# 两者中标签的顺序不需要保持一致
# 
# 更详细的解释内容，请参阅 rules_*.sh 文件！
#
RULESET_URLS(
  "HK_IPv4_ruleset01|
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/google.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/google-cn.srs"

  "HK_IPv6_ruleset02|
  https://github.com/KaringX/karing-ruleset/raw/sing/geo/geosite/google-trust-services@cn.srs"

  "US|
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-openai.srs
  https://github.com/SagerNet/sing-geosite/raw/rule-set/geosite-bing.srs
  https://github.com/KaringX/karing-ruleset/raw/sing/geo/geoip/ai.srs"

  "Others|
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/telegram.srs"

  "my_cn_direct|
  /etc/homeproxy/ruleset/MyDirect.json"

  "reject_out|/etc/homeproxy/ruleset/adblockdns.srs"

  "direct_out|
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geoip/cn.srs
  https://github.com/MetaCubeX/meta-rules-dat/raw/sing/geo/geosite/cn.srs"
)

DNS_SERVERS=(
  "HK_IPv4_ruleset01_opendns|https://doh.opendns.com/dns-query"
  "HK_IPv6_ruleset02_CF|https://1.1.1.1/dns-query"
  "US_California_Outbound|https://dns.google/dns-query"
  "Others|URL"
  "my_cn_direct|URL"

  "default_server|URL(s)"
)
```

<br/>

<br/>

### <a name="param-description">参数说明</a>
`注意：以下3个列表的内部 '标签名' --> 不可重复！！！且标签名仅支持英文大小写、下划线_、数字的单一或组合形式。`

<br/>

#### SUBSCRIPTION_URLS
机场或代理服务器订阅链接(可选)。

格式为： `"URL#自定义标签名"`。其中 '自定义标签名' 支持中文，但需要确保脚本的编码为 `UTF-8`.

* 如果你提供了链接，那么脚本会在运行时自动帮你添加、订阅、生成节点到 Homeproxy 中。
* 如果你不想使用此功能，直接删除整个 `SUBSCRIPTION_URLS=(xxx)` 代码块即可。

`提示：你提供的链接将仅用于调用 Homeproxy 订阅功能快速生成节点信息，不会出现隐私安全问题，请放心使用！`

```bash
SUBSCRIPTION_URLS=(
  "https://abc.xyz/subscribe?token=123#机场1"
  "https://yhb.com/subscribe?token=uhjk#机场2"
  # More...
)
```

<br/>

#### RULESET_URLS

规则集列表。

格式为：`标签名|URL(s)`

* `direct_out(直连)` 和 `reject_out(广告&隐私)` 为保留标签名称不可更改，但如果不想使用它们，可直接删除整个 `direct_out` 或(和) `reject_out` 行所有内容
* `标签名` 及其内 `URL(s)` 的顺序可以随意调整，可以随意添加、修改、删除，`但同一条规则集url在整个 RULESET_URLS(xxx) 数组中只允许出现一次`
* 脚本会自动根据你提供的 url 是否包含 `geosite`/`geoip`/`ip` 字符来判断它们是域名规则还是IP规则
* `URL(s)` 支持远程URL & 本地绝对路径、`.srs` 和 `.json` 类型文件



> 1. `标签名` 的顺序即为界面 `路由节点(Routing Nodes)`、`路由规则(Routing Rules)`、`DNS规则(DNS Rules)` 功能中的条目优先级顺序
> 2. 标签名中的 `URL(s)` 的顺序(从上到下)：
>    1. 即为 `路由规则(Routing Rules)`、`DNS规则(DNS Rules)` 每个条目中选中的 `规则集(Rule Set)` 选项的优先级顺序，代表规则匹配次序
>    2. 即为 `规则集(Rule Set)` 功能中的条目优先级顺序，代表每个规则集文件的加载、下载次序



以下三种写法任选其一。

##### 写法一：按照节点分组

参考 [rules_based_on_nodes.sh](https://github.com/thisIsIan-W/homeproxy-autogen-configuration/blob/main/rules_based_on_nodes.sh) 文件。

##### 写法二：按照规则集合分组

参考 [rules_based_on_rulesets.sh](https://github.com/thisIsIan-W/homeproxy-autogen-configuration/blob/main/rules_based_on_rulesets.sh) 文件。

##### 写法三：按照机场分组

参考 [rules_based_on_proxy_servers.sh](https://github.com/thisIsIan-W/homeproxy-autogen-configuration/blob/main/rules_based_on_proxy_servers.sh) 文件。

<br/>


#### DNS_SERVERS
DNS服务器列表，在这里配置你想要使用的 ***DNS服务商***。

格式为：`标签名|URL(s)`

* DNS 服务器可随意增删修改、调整顺序
* 同一条 URL 可以在同一个标签内 或 多个标签内多次出现
* URL(s) 支持: UDP, TCP, DoT, DoH, and RCode



> 1. 所有的 `DNS服务商` 会默认选中 `IPv4` 作为解析策略，如需要使用 `IPv6`，请手动到界面选择
> 2. `标签名` 的顺序即为 `DNS服务器(DNS Servers)` 中每个条目的次序
> 3. 标签名中的多个 `URL(s)` 会自动从 1 开始为你自动生成相同名称前缀的、多个不同链接的DNS服务商。参考下方示例



```txt
DNS_SERVERS=(
  # 会生成一条名称为 dns_server_google 的 DNS服务商
  "google|url"

  # 会生成3条名称分别为 dns_server_cloudflare_1、dns_server_cloudflare_2、dns_server_cloudflare_3 的 DNS服务商
  # 且 url 会按照顺序分配给上述3个服务商
  "cloudflare|
  url1
  url2
  url3"

  # More...
)
```

<br/>

<br/>

#### 错误写法
适用于 `RULESET_URLS` 以及 `DNS_SERVERS`.

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
