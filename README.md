# EchoFS 汉化版 (echofs-zh)

基于 [dengsgo/echofs](https://github.com/dengsgo/echofs) **v1.6.0** 的中文汉化版。
Web 界面全面中文化,**musl 静态链接**编译,单文件二进制、零运行时依赖,开箱即用。

适用于普通 Linux 以及 **glibc 较旧的精简环境**(BusyBox / NAS / 路由器 / 低配小主机等),
即使原版因 GLIBC 版本过旧无法运行,本版也能直接跑。

## 下载

前往 [Releases](https://github.com/zhufanshao/echofs-zh/releases) 下载最新版:

| 文件 | 平台 | 说明 |
| --- | --- | --- |
| `echofs-linux-amd64-zh` | Linux x86_64 | musl 静态链接,glibc 2.20 旧环境也能直接运行 |
| `echofs-windows-x86_64-zh.exe` | Windows x86_64 | 命令行或双击运行 |
| `echofs-macos-arm64-zh` | macOS Apple Silicon (M 系列) | 原生 arm64 版本 |
| `echofs-macos-x64-zh` | macOS Intel | x86_64 版本 |

校验值(SHA256,下载后请核对):

```bash
echo "E844D6E2AA567F43A76C4C369512EB88CC0130CD5DB51368F2B02A1DB9776AFE  echofs-linux-amd64-zh" | sha256sum -c -
echo "F7BE0D80138AEE36771D2E1E9F1D27A3153B2928DBC6B26D31315F87A8F310CB  echofs-windows-x86_64-zh.exe" | sha256sum -c -
echo "3B6471CEB7DA9BA5B1C041A7AA8C58637A0AD88109C8182966B768C11015A4A4  echofs-macos-arm64-zh" | sha256sum -c -
echo "04DE320417E63D11F147EDB802639EC3C9982C36D6A4D0C9B06B767C80BE478B  echofs-macos-x64-zh" | sha256sum -c -
```

## 使用方法

```bash
# Linux / macOS
chmod +x echofs-*-zh
./echofs-linux-amd64-zh -r .
./echofs-macos-arm64-zh -r /data/share -p 8080

# Windows
echofs-windows-x86_64-zh.exe -r D:\share -p 8080
```

- 浏览器访问 `http://IP:8080` 即可浏览 / 上传 / 下载文件
- Windows 资源管理器地址栏输入 `\\IP@8080\` 可挂载为 WebDAV 网络驱动器
- 完整参数见 `./echofs-xxx --help`

> **macOS 提示**:二进制未签名,首次运行时如被 Gatekeeper 拦截,
> 在终端执行 `xattr -d com.apple.quarantine ./echofs-macos-*-zh` 后即可打开。

## 构建方法(从源码)

```bash
rustup target add x86_64-unknown-linux-musl
cargo build --release --target x86_64-unknown-linux-musl
```

本仓库自带 GitHub Actions 工作流(`.github/workflows/build-macos.yml`),
每次推送 `main` 会自动在 macOS 官方构建机上编译并上传双架构版本。

## 与上游的差异

- Web 界面文本全部中文化(`src/template.js` / `src/template.rs`)
- 错误页与服务器错误信息中文化(`src/error.rs` / `src/handlers.rs`)
- 无其他功能改动

## 致谢

- 原作者 [dengsgo](https://github.com/dengsgo) 的 [echofs](https://github.com/dengsgo/echofs) 项目

## License

MIT(同上游 echofs)
