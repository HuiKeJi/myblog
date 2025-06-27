---
title: 安装nftables
date: 2025-6-5
category: linux
tag: firewall
index_img: /img/qw1124.png
---

## 防火墙配置，确定防火墙是否存在

`sudo pacman -S nftables 正常已经内置无需安装`
`sudo pacman -Q nftables 查看是否存在`

# 打开服务

`sudo systemctl enable --now nftables.seriver`
`sudo systemctl status nftables.seriver 查看服务是否开启`

# 所有的放行和禁止端口都在/etc/nftables.conf下设置

### 修改后的配置（在你的基础上扩展端口规则）

# ----------------------------------------------------

```conf
destroy table inet filter
table inet filter {
  chain input {
    type filter hook input priority filter
    policy drop

ct state invalid drop comment "early drop of invalid connections"
ct state {established, related} accept comment "allow tracked connections"
iif lo accept comment "allow from loopback"
ip protocol icmp accept comment "allow icmp"
meta l4proto ipv6-icmp accept comment "allow icmp v6"

# 开放 SSH

tcp dport ssh accept comment "allow sshd"

# 开放你需要的端口（TCP 和 UDP）（{}这是一个集合）
tcp dport { 443, 80, 8080, 8090, 1090, 1080, 33612, 23361, 5000, 2017, 10170, 1314 } accept comment "allow specified tcp ports"
udp dport { 443, 80, 8080, 8090, 1090, 1080, 33612, 23361, 5000, 2017, 10170, 1314 } accept comment "allow specified udp ports"

# kdeconnect
tcp dport 1716-1764 accept comment "allow kdeconnect tcp ports"
udp dport 1716-1764 accept comment "allow kdeconnect udp ports"

# 限制扫描类数据包
pkttype host limit rate 5/second counter reject with icmpx type admin-prohibited

# 统计默认拒绝包

counter

  }

  chain forward {
    type filter hook forward priority filter
    policy drop
  }
}
```

# ----------------------------------------------------

> #### 将此内容保存为/etc/nftables.conf

#### 重载防火墙规则：
`sudo systemctl restart nftables.seriver`


# 为什么不放行端口也能访问？
#### 访问分为「出站」连接，「入站」连接

> Linux 防火墙默认是作用在「入站（input）」流量上的，浏览器访问网页属于「出站（output）」流量。

## 情况对比

###### 使用方式	属于什么方向	是否被 input 链限制

- 浏览器访问网页（代理转发）	出站（output）	❌ 不受限制
- 服务监听本地端口供外部访问	入站（input）	✅ 会被限制
- v2ray 被客户端访问（例如手机连）	入站（input）	✅ 会被限制


1. 情况 A：你访问外网网页
2. curl -x 127.0.0.1:2017 https://example.com
3. 请求从本地发出，走的是 output 链
4. 即使你没开放 2017 端口，也能用
5. 因为 你在本机发请求，本机响应自己，或是本机作为客户端连接远端服务器


1. 情况 B：别人连接你的 2017 端口
2. 比如你手机设置代理指向：
3. 你的电脑IP:2017
4. 这时手机的连接请求会命中 input 链，而你：
5. 如果没有 tcp dport 2017 accept，防火墙就会拦住这次连接
6. ❌ 所以不能访问

