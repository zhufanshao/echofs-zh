# EchoFS 汉化版 (echofs-zh)

基于 [dengsgo/echofs](https://github.com/dengsgo/echofs) **v1.6.0** 的中文汉化版。
Web 界面全面中文化,**musl 静态链接**编译,单文件二进制、零运行时依赖,开箱即用。

适用于普通 Linux 以及 **glibc 较旧的精简环境**(BusyBox / NAS / 路由器 / 低配小主机等),
即使原版因 GLIBC 版本过旧无法运行,本版也能直接跑。

## 特性

- Web 界面全面中文化:上传 / 下载 / 预览 / 重命名 / 移动 / 删除 / ZIP 打包下载 / 二维码分享
- 文件浏览(列表 / 网格视图)、排序、拖拽上传、多文件并发上传
- 支持 WebDAV,可挂载为 Windows 网络驱动器、手机 / 电视 / 网盘客户端
- 内置 HTTP 认证(用户名 / 密码)、目录隐藏、下载限速、访客只读等选项
- 单文件、零依赖、静态链接,拷过去就能用

## 下载

前往 [Releases](https://github.com/zhufanshao/echofs-zh/releases) 下载最新版:

| 文件 | 说明 |
| --- | --- |
| `echofs-linux-amd64-zh` | Linux x86_64(musl 静态链接,推荐) |

校验值(请核对,防止文件损坏或篡改):

```
SHA256: E844D6E2AA567F43A76C4C369512EB88CC0130CD5DB51368F2B02A1DB9776AFE
MD5:    F07C3B17BCBD29C38F68483B15C1780A
```

```bash
echo "E844D6E2AA567F43A76C4C369512EB88CC0130CD5DB51368F2B02A1DB9776AFE  echofs-linux-amd64-zh" | sha256sum -c -
```

## 使用方法

```bash
chmod +x echofs-linux-amd64-zh
# 共享当前目录
./echofs-linux-amd64-zh -r .
# 共享指定目录并指定端口
./echofs-linux-amd64-zh -r /data/share -p 8080
# 绑定地址 + 开启登录认证
./echofs-linux-amd64-zh -r /data/share -b 0.0.0.0 -u admin -p password
```

- 浏览器访问 `http://IP:8080` 即可浏览 / 上传 / 下载文件
- Windows 资源管理器地址栏输入 `\\IP@8080\` 可挂载为 WebDAV 网络驱动器
- 完整参数见 `./echofs-linux-amd64-zh --help`

## 构建方法(从源码)

```bash
rustup target add x86_64-unknown-linux-musl
cargo build --release --target x86_64-unknown-linux-musl
```

## 与上游的差异

- Web 界面文本全部中文化(`src/template.js` / `src/template.rs`)
- 错误页与服务器错误信息中文化(`src/error.rs` / `src/handlers.rs`)
- 无其他功能改动

## 致谢

- 原作者 [dengsgo](https://github.com/dengsgo) 的 [echofs](https://github.com/dengsgo/echofs) 项目

## License

MIT(同上游 echofs)
