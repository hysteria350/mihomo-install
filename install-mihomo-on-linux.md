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

先进行[订阅购买]() ，获取到订阅链接。订阅链接位于：仪表盘 > 一键订阅 , 然后复制订阅地址, 后面配置文件需要使用到。

参考以下配置修改/etc/mihomo/config.yaml， 将以下配置中，订阅一的url替换成自己购买的订阅地址。

>  更多配置选项参考 [mihomo-config-example.yml](mihomo-config-example.yml)

```yaml
port: 7890
socks-port: 7891
allow-lan: true
mode: rule
log-level: info

# 外部控制端口
external-controller: 0.0.0.0:9090
external-ui: ui
# 如果本地可以直接访问github则直接通过github下载UI
external-ui-url: 'https://github.com/Zephyruso/zashboard/releases/latest/download/dist.zip'
secret: "yyhhyyyyyy"

dns:
  enable: true
  listen: 0.0.0.0:53
  enhanced-mode: fake-ip
  nameserver:
    - 1.1.1.1
    - 8.8.8.8

# 延迟检测 URL
p: &p
  type: http
  # 自动更新订阅时间，单位为秒
  interval: 3600
  health-check:
    enable: true
    url: https://cp.cloudflare.com
    # 节点连通性检测时间，单位为秒
    interval: 300
    # 节点超时延迟，单位为毫秒
    timeout: 1000
    # 节点自动切换差值，单位为毫秒
    tolerance: 100

# 订阅名，记得修改成自己的
# 添删订阅在这里和下方订阅链接依葫芦画瓢就行
use: &use
  # 如果不希望自动切换请将下面两行注释对调
  # type: select
  type: url-test
  use:
    - 订阅一
    # - 本地配置

proxy-providers:
  订阅一:
    <<: *p
    # path: ./proxy_provider/订阅一.yaml
    url: "https://example.com/subscribe?token=xxxxxx"
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
  # 使用 WARP 的用户需要手动在下方的 proxies 字段内添加 WARP
  # 例如 [WARP, 全部节点, 自动选择, 香港, 台湾, 日本, 新加坡, 美国, 其它地区, DIRECT],
  - {
      name: 节点选择,
      type: select,
      proxies:
        [全部节点],
    }
  - { name: 全部节点, <<: *use }

rules:
  - DOMAIN-SUFFIX,google.com,节点选择
  - GEOIP,CN,DIRECT
  - MATCH,节点选择
```

## 安装控制面板

控制面板已由external-ui-url属性指定, mihomo会根据的url下载指定的控制面板， 网络状况不佳可以通过代理， 例如：ghfast.top 下载。

```code
# 如果本地可以直接访问github则直接通过github下载UI
external-ui-url: 'https://github.com/Zephyruso/zashboard/releases/latest/download/dist.zip'
# 如果本地不能直接访问github则通过代理下载UI
# external-ui-url: 'https://ghfast.top/https://github.com/Zephyruso/zashboard/releases/latest/download/dist.zip'
# metacubexd theme
# external-ui-url: 'https://ghfast.top/https://github.com/MetaCubeX/metacubexd/archive/refs/heads/gh-pages.zip'
```

现在重新mihomo服务， 测试一下整个流程, 应该一切正常。现在可以打开 Dashboard: <http://127.0.0.1:9090/ui> 查看相应信息并进行管理。

```bash
systemctl restart mihomo
```

## 参考文档

[Clash on Windows](https://senzyo.net/2023-7/)

[Mihomo 使用教程（2025 最新版）](https://www.clash.la/archives/976/)


