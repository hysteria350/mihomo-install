# Windows 平台上 Mihomo 安装和使用教程（2025 最新版）

如果你正在寻找一个高性能的代理核心工具用于科学上网、规则分流等，Mihomo 是目前最值得推荐的选择之一。本文将教你如何在 Windows 平台下载、 安装、配置并结合控制面板使用 Mihomo。

Mihomo 是由 MetaCubeX 团队发布的 Clash.Meta 核心的官方构建版本，它是 Clash Premium 的开源替代方案，支持以下功能：

- 规则分流（rule-set）
- 协议嗅探（sniffing）
- TUN 模式
- 脚本规则（script）
- 完整 Clash Premium 功能

> 注意：Mihomo 是命令行工具，不自带图形界面，可搭配控制面板或 GUI 客户端使用。

## 下载 Mihomo

GitHub 官方地址：<https://github.com/MetaCubeX/mihomo>

根据操作系统类型，下载相应的 mihomo 版本，本文实验环境为windows， cpu为x86-64架构所以下载的版本为 mihomo-windows-amd64.exe。

## 配置文件

创建 mihomo 工作目录, 比如 C:\Users\<UserName>\Apps\mihomo。 放入配置文件 config.yaml。
参考以下配置:

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

proxies:
  - name: 🇯🇵 日本节点
    type: vmess
    server: jp.example.com
    port: 443
    uuid: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    cipher: auto
    tls: true

proxy-groups:
  - name: 🚀 节点选择
    type: select
    proxies:
      - 🇯🇵 日本节点

rules:
  - DOMAIN-SUFFIX,google.com,🚀 节点选择
  - GEOIP,CN,DIRECT
  - MATCH,🚀 节点选择
```

## 启动与设置为开机自启动

开机自启动

### 方案一: 任务计划程序
打开任务计划程序, 点击右侧操作列表中的“创建任务”:

1. 常规

名称填 mihomo。 更改用户或组, 输入 system, 点击 检查名称 按钮, 变为 SYSTEM, 即使用 SYSTEM 账户。

2. 触发器

新建, 开始任务 选择 登录时 或 启动时, 按需选择 延迟任务时间, 确认勾选 已启用。

3. 操作

新建, 操作 选择 启动程序; 程序或脚本 填写 mihomo.exe; 添加参数 填写 -d .\ -f config.yaml; 起始于 填写 mihomo 工作目录, 比如 C:\Users\<UserName>\Apps\mihomo。

4. 条件

全部取消勾选, 包括灰显的。

5. 设置

仅勾选 允许按需运行任务。
最下方的 如果此任务已经运行, 以下规则适用, 确认选择 请勿启动新实例

### 方案一: Windows 服务

利用 winsw 安装 Windows 服务。

1. 将以下内容保存为 mihomo service.xml, 和 winsw.exe 一起放入 mihomo 工作目录中。

```xml
<service>
    <startmode>Automatic</startmode>
    <id>mihomo service</id>
    <name>mihomo 服务</name>
    <description></description>
    <executable>mihomo.exe</executable>
    <arguments>-d .\ -f config.yaml</arguments>
    <log mode="none"></log>
</service>
```
> 注意, <id> 指 Name, <name> 指 DisplayName。更多 XML 设置参考 文档。

2. 在 mihomo 工作目录中运行以下命令将 mihomo service 安装为 Windows 服务:

```powershell
winsw.exe install "mihomo service.xml"
```

3. 运行服务, 检查是否正常。

## 安装控制面板

下载 [Yacd-meta](https://ghproxy.net/https://github.com/MetaCubeX/Yacd-meta/archive/refs/heads/gh-pages.zip) 或者 [MetaCubeXD](https://ghproxy.net/https://github.com/MetaCubeX/metacubexd/archive/refs/heads/gh-pages.zip), 解压后将文件夹重命名为 ui, 移动到 mihomo 工作目录下: C:\Users\<UserName>\Apps\mihomo\ui。

现在可以运行 任务计划程序 或 Windows 服务 测试一下整个流程, 应该一切正常。现在可以打开 Dashboard: <http://127.0.0.1:9090/ui> 查看相应信息并进行管理。

> 智能多宿主名称解析与防火墙
> 注意, 在 Windows 中, 如果在 TUN 配置中启用了严格路由,
> 
> 1. 不必手动启用组策略中 计算机配置→管理模板→网络→DNS 客户端→禁用智能多宿主名称解析 这一策略了, 保持这一策略为 未配置/已禁用 即可。
> 2. 不必手动为 mihomo 配置 Windows 防火墙, mihomo 会自动配置的。
> 3. 不然, 你会发现虽然测速延迟还可以, 但实际速度很慢。
> 4. 由于 mihomo 和 sing-box 都使用 sing-tun, 所以上述注意事项同样适用于 sing-box。

## 参考文档

[Clash on Windows](https://senzyo.net/2023-7/)

[Mihomo 使用教程（2025 最新版）](https://www.clash.la/archives/976/)


