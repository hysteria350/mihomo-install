# Linux 平台上 Mihomo 安装和使用教程（2025 最新版）

如果你正在寻找一个高性能的代理核心工具用于科学上网、规则分流等，Mihomo 是目前最值得推荐的选择之一。本文将教你如何在 Linux 平台下载、 安装、配置并结合控制面板使用 Mihomo。

Mihomo 是由 MetaCubeX 团队发布的 Clash.Meta 核心的官方构建版本，它是 Clash Premium 的开源替代方案，支持以下功能：

- 规则分流（rule-set）
- 协议嗅探（sniffing）
- TUN 模式
- 脚本规则（script）
- 完整 Clash Premium 功能

> 注意：Mihomo 是命令行工具，不自带图形界面，可搭配控制面板或 GUI 客户端使用。

## 下载并安装 Mihomo

GitHub 官方地址：<https://github.com/MetaCubeX/mihomo>
Mihomo 官网: <https://wiki.metacubex.one/> 

根据操作系统类型，下载相应的 mihomo 版本，如何选择版本参考[这里](https://wiki.metacubex.one/startup/faq/#_2) ，本文实验环境为Alma Linux 9， cpu为x86-64架构所以下载的版本为 [mihomo-linux-amd64](https://github.com/MetaCubeX/mihomo/releases/download/v1.19.12/mihomo-linux-amd64-v3-v1.19.12.rpm)。

```bash

# 下载 mihomo
user@server$ curl --output /tmp/mihomo-linux-amd64-v3-v1.19.12.rpm -O https://github.com/MetaCubeX/mihomo/releases/download/v1.19.12/mihomo-linux-amd64-v3-v1.19.12.rpm
# 安装 mihomo
user@server$ sudo rpm -i  /tmp/mihomo-linux-amd64-v3-v1.19.12.rpm

# 查看安装了哪些文件

user@server$ rpm -ql mihomo
/etc/mihomo/config.yaml
/usr/bin/mihomo
/usr/lib/systemd/system/mihomo.service
/usr/lib/systemd/system/mihomo@.service
/usr/share/licenses/mihomo/LICENSE

# 设置开机自启动并立即启动mihomo
user@server$ sudo systemctl enable --now mihomo

```

## 配置文件

将配置文件 config.yaml 放到 Mihomo 同目录下，参考以下配置:

>  更多配置选项参考 [mihomo-config-example.yml](mihomo-config-example.yml)

```yaml
port: 7890
socks-port: 7891
allow-lan: true
mode: rule
log-level: info

external-controller: 127.0.0.1:9090
external-ui: ui
secret: ""

dns:
  enable: true
  listen: 0.0.0.0:53
  enhanced-mode: fake-ip
  nameserver:
    - 1.1.1.1
    - 8.8.8.8
proxy-providers:
  订阅一:
    <<: *p
    # path: ./proxy_provider/订阅一.yaml
    url: "https://example.com/airport?token=xxxxxxx"
    # 如需要为该订阅组节点添加前缀，取消下面两行注释
    # override:
      # additional-prefix: "[订阅一]"

# proxies:
#  - name: 🇯🇵 日本节点
#    type: vmess
#    server: jp.example.com
#    port: 443
#    uuid: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
#    cipher: auto
#    tls: true

proxy-groups:
  - name: 🚀 节点选择
    type: select
    proxies:
      - 🇯🇵 日本节点

# 外部控制端口
external-controller: 0.0.0.0:9090
external-ui: ui
# 如果本地可以直接访问github则直接通过github下载UI
external-ui-url: 'https://github.com/Zephyruso/zashboard/releases/latest/download/dist.zip'
secret: "yyhhyyyyyy"

rules:
  - DOMAIN-SUFFIX,google.com,🚀 节点选择
  - GEOIP,CN,DIRECT
  - MATCH,🚀 节点选择
```


## 安装控制面板

现在重新mihomo服务， 测试一下整个流程, 应该一切正常。现在可以打开 Dashboard: <http://127.0.0.1:9090/ui> 查看相应信息并进行管理。

```bash
systemctl restart mihomo
```

## 参考文档

[Clash on Windows](https://senzyo.net/2023-7/)

[Mihomo 使用教程（2025 最新版）](https://www.clash.la/archives/976/)


