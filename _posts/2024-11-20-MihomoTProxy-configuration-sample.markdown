---
layout:     post
title:      "MihomoTProxy 多机场出站通用配置模板"
subtitle:   "mihomotproxy 配置模板"
date:       2024-10-30
author:     "Ian"
header-img: "img/post-bg-js-module.jpg"
tags:
    - 代理工具
    - MihomoTProxy
    - 配置教程
---



多机场用户配置模板：[https://gist.github.com/thisIsIan-W/31ed4fdd3dd7a3185fba824ed891e4cb](https://gist.github.com/thisIsIan-W/31ed4fdd3dd7a3185fba824ed891e4cb)

<br/>

<br/>

## 通用配置说明

### proxy-providers

```yaml
proxy-providers:
  # airport_01 与 airport_02 可自定义名称
  # 支持 emoji 表情符号以及中英文
  airport_01:
    url: "" # 在这里补全你的机场链接
  airport_02:
    url: "" # 在这里补全你的机场链接
  # 省略其它配置
```

<br/>

### proxy-groups

```yaml
# 机场通用出站锚点
# 可使用如 vscode 等工具一键修改 airport_01/airport_02 前缀为你想要的名称
airport_01_proxy_group: &airport_01_proxy_group
  type: select
  proxies:
    - 🟢 直连
    - 🚫 拒绝
    - ✌️ airport_01常用
    - 🌐 airport_01其它
    - ♻️ airport_01自动选择
    - 🍉 airport_02常用
    - ⚾ airport_02其它
    
# 然后在 proxy-groups 中引用它：
proxy-groups:
  - name: airport_01出口 # 自定义出口名称，会显示在 Clash UI 中的 "代理(Proxies)" 页面
    <<: *airport_01_proxy_group # 引用上方 &airport_01_proxy_group 处定义的所有配置
```

<br/>

### rule-providers

```yaml
# 通用配置锚点
rule-universal-definition:
  universal-config: &universal
    type: http
    interval: 86400
  domain: &domain # 第一个 domain 表示这条配置的名称，第二个 &domain 表示引用符号，可在其它配置中通过 "<<: *domain" 来引用它的配置
    <<: *universal # 引用 &universal 处的配置，也就是会把上述 [type: http, interval: 86400] 这两条配置引入到此处
    behavior: domain
    format: mrs
rule-providers:
  github:
    <<: *domain # 引用上方 &domain 处定义的全部配置
    url: "https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/refs/heads/meta/geo/geosite/github.mrs"
    
# 你也可以不使用锚点功能，直接以传统方式定义配置
rule-providers:
  github:
    type: http
    interval: 86400
    behavior: domain
    format: mrs
    url: "https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/refs/heads/meta/geo/geosite/github.mrs"
```

