# EchoFS 汉化版使用教程

单文件、零依赖的 HTTP 文件服务器 + WebDAV 服务器。下载对应平台的二进制后即可使用。

## 快速开始

### Linux

```bash
chmod +x echofs-linux-amd64-zh
# 共享当前目录(默认端口 8080)
./echofs-linux-amd64-zh
# 共享指定目录、指定端口
./echofs-linux-amd64-zh -r /data/share -p 8080
# 绑定所有网卡、限制下载速度、关闭日志
./echofs-linux-amd64-zh -r /data/share -b 0.0.0.0 -s 5m -l off
```

### Windows

```powershell
# 双击 echofs-windows-x86_64-zh.exe,或命令行:
.\echofs-windows-x86_64-zh.exe -r D:\share -p 8080
# 启动后自动打开浏览器(可选):
.\echofs-windows-x86_64-zh.exe -r D:\share -o
```

> 首次运行如弹出防火墙提示,请允许"专用网络"访问,否则局域网设备无法连接。

### macOS

```bash
chmod +x echofs-macos-arm64-zh   # Apple Silicon 用 arm64 版
chmod +x echofs-macos-x64-zh     # Intel Mac 用 x64 版
./echofs-macos-arm64-zh -r ~/Public -p 8080
```

> **Gatekeeper 提示**:二进制未签名,首次运行若提示"无法打开",执行一次:
> `xattr -d com.apple.quarantine ./echofs-macos-arm64-zh` 后即可正常运行。

启动后,浏览器访问 `http://服务器IP:8080` 即可浏览 / 上传 / 下载文件。

## 完整命令行参数

| 参数 | 说明 | 默认值 |
| --- | --- | --- |
| `-r, --root <目录>` | 要共享的根目录 | 当前目录 `.` |
| `-p, --port <端口>` | 监听端口 | `8080` |
| `-b, --bind <地址>` | 绑定地址(`0.0.0.0` 表示所有网卡) | `0.0.0.0` |
| `-o, --open` | 启动后自动打开浏览器 | 关闭 |
| `-H, --show-hidden` | 显示隐藏文件/目录(以 `.` 开头) | 关闭 |
| `-d, --max-depth <N>` | 目录浏览最大深度(`-1` 不限) | `-1` |
| `-l, --log <模式>` | 访问日志:`stdout` / `off` / 文件路径 | `stdout` |
| `-s, --speed-limit <限速>` | 每请求下载限速,如 `500k`、`1m`、`10m` | 不限速 |
| `--no-webdav` | 关闭 WebDAV(PROPFIND)支持 | WebDAV 开启 |
| `--webdav-user <用户名>` | 设置 WebDAV 用户名,开启 WebDAV 基础认证(不影响网页) | 无 |
| `--webdav-pass <密码>` | WebDAV 密码(配合 `--webdav-user`) | 无 |
| `--webui-auth` | 网页浏览/下载也要求登录(复用 WebDAV 账号密码) | 关闭 |

常用示例:

```bash
# 共享 + 开启网页登录认证(网页和 WebDAV 都要账号密码)
./echofs-linux-amd64-zh -r /data -p 8080 --webdav-user admin --webdav-pass secret --webui-auth

# 只读共享 + 限速
./echofs-linux-amd64-zh -r /data -s 2m

# 隐藏文件不显示 + 目录深度限制 3 层
./echofs-linux-amd64-zh -r /data -H -d 3
```

## WebDAV 挂载(把服务器当网盘用)

服务默认开启 WebDAV,HTTP 端口即 WebDAV 端口,地址为 `http://服务器IP:端口/`。

### Windows 资源管理器

1. 打开"此电脑" → 右键"网络" → "映射网络驱动器"
2. 文件夹填:`\\服务器IP@8080\`(注意 `@` 代替端口前的冒号)
3. 如设置了账号密码,勾选"使用其他凭据"后输入 `--webdav-user` / `--webdav-pass` 对应的账号

> 也可以直接在资源管理器地址栏输入 `\\服务器IP@8080\` 回车。

### macOS Finder

1. 菜单栏"前往" → "连接服务器"(快捷键 `Cmd+K`)
2. 地址填:`http://服务器IP:8080/`
3. 输入账号密码(如已设置)后即可像本地文件夹一样访问

### Linux(davfs2)

```bash
sudo apt install davfs2          # Debian/Ubuntu
sudo mount -t davfs http://服务器IP:8080/ /mnt/echofs
```

### 手机

使用支持 WebDAV 的文件管理器 / 网盘客户端(如 ES 文件浏览器、nPlayer、Infuse、Solid Explorer 等),
新建连接时选 **WebDAV**,地址填 `http://服务器IP:8080/` 即可。

## 开机自启

### Linux systemd(推荐)

```ini
# /etc/systemd/system/echofs.service
[Unit]
Description=EchoFS file server
After=network.target

[Service]
ExecStart=/usr/local/bin/echofs-linux-amd64-zh -r /data/share -p 8080 --webdav-user admin --webdav-pass secret
Restart=on-failure
User=root

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable --now echofs
```

### 精简 Linux(init.d / BusyBox)

```sh
# /etc/init.d/S90echofs
#!/bin/sh
DAEMON=/var/fs_disk/echofs-linux-amd64-zh
case "$1" in
  start) $DAEMON -r /data/share -p 8080 >/dev/null 2>&1 & ;;
  stop)  killall echofs-linux-amd64-zh ;;
esac
```

```bash
chmod +x /etc/init.d/S90echofs
```

### Windows

1. 在"启动"文件夹(`Win+R` 输入 `shell:startup`)放一个快捷方式,指向:
   `echofs-windows-x86_64-zh.exe -r D:\share -p 8080`
2. 或用"任务计划程序"创建开机任务,更稳定。

### macOS(launchd)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key><string>com.echofs.zh</string>
  <key>ProgramArguments</key>
  <array>
    <string>/usr/local/bin/echofs-macos-arm64-zh</string>
    <string>-r</string><string>/Users/Shared</string>
    <string>-p</string><string>8080</string>
  </array>
  <key>RunAtLoad</key><true/>
  <key>KeepAlive</key><true/>
</dict>
</plist>
```

```bash
cp com.echofs.zh.plist ~/Library/LaunchAgents/
launchctl load ~/Library/LaunchAgents/com.echofs.zh.plist
```

## 常见问题

### 1. 外网访问不了?

- 确认服务绑定的是 `0.0.0.0`(默认),而不是 `127.0.0.1`
- 确认防火墙/安全组放行了对应端口
- 家庭宽带需要路由器做**端口转发**,且运营商可能封了 80/443 等常用端口,建议用 8080 等非常用端口

### 2. macOS 提示"无法打开,因为无法验证开发者"?

二进制未签名导致,执行:

```bash
xattr -d com.apple.quarantine ./echofs-macos-*-zh
```

### 3. Windows 防火墙弹窗?

首次运行选择"允许访问",或手动放行 TCP 端口:

```powershell
netsh advfirewall firewall add rule name="echofs" dir=in action=allow protocol=TCP localport=8080
```

### 4. 端口被占用?

换端口或先找占用进程:

```bash
# Linux
ss -tlnp | grep 8080
# Windows
netstat -ano | findstr 8080
```

### 5. 上传/下载大文件很慢?

用 `-s` 参数可限制(而非加速);加速请确保带宽充足,或换用有线网络。
同时支持多文件并发上传,拖拽多个文件到页面即可。

### 6. 只想让别人下载、不能删除/重命名?

目前 echofs 未提供"只读"开关;可以通过设置 WebDAV 账号密码并让网页匿名访问来限制写入(仅 WebDAV 需要认证):
`--webdav-user admin --webdav-pass secret`(网页保持匿名,WebDAV 需登录)。
