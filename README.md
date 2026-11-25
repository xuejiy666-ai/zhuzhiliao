<div align="center">
  <img src="icon-512.png" width="120" alt="竹知了">
  <h1>竹知了 · 一转就哇哇叫</h1>
  <p>竹知了（竹蝉、拉线蝉、知了哇呜）的网页版模拟 —— 按住屏幕画圈甩起来，转得越快叫得越响。</p>
  <h3><a href="https://xuejiy666-ai.github.io/zhuzhiliao/">▶ 点这里在线玩</a></h3>
  <p><sub>https://xuejiy666-ai.github.io/zhuzhiliao/　手机、电脑直接打开，无需下载</sub></p>
</div>

## 这是什么

竹知了是一种传统民间玩具：竹筒一端蒙上竹膜，膜心系一根涂了松香的线，另一头拴在小竹签上。抓住竹签甩起来转圈，线在松香上“黏—滑”交替摩擦，振动顺着线传到竹膜，再由竹筒空腔共鸣，发出酷似知了的叫声。

这个项目把它搬进浏览器：Canvas 画出现场（竹影、月亮、流萤、绳与竹筒），声音来自真实竹知了的录音采样，转速、张力、音量全部实时联动。

单页前端，**无构建、无依赖、无后端**，纯静态托管即可运行。

## 玩法

- 手指或鼠标**按住画布画圈**，指针带着甩杆转，线被绷紧后竹筒开始发音。
- 转速越快，音调越高、音量越大；面板里有实时「圈 / 秒」表。
- 松手后不再有外力输入，竹筒按重力 + 弹性绳 + 阻力的物理自行减速停摆。
- 两种替代操作：**自动甩**（自动匀速画圈）、**甩手机**（握手机在空中划圈，靠 DeviceMotion 重力方向驱动，iOS 需授权）。

## 实现要点

| 模块 | 说明 |
| --- | --- |
| 物理 | 甩杆（指针）→ 绳系竹筒（质点，重力 + 弹性绳 + 阻力），定步长积分 |
| 发声 | 绳方向角速度驱动：0–7 圈/秒线性映射到录音 0.5–1.66 倍速播放，超出钳制；采样写死在 `audio-data.js`，WebAudio 解码后随机切换，避免重复感 |
| 兜底 | 采样不可用时退化为 WebAudio 合成音 |
| 音频健壮性 | iOS 从后台切回后上下文“状态 running 却无声”，靠 `currentTime` 心跳检测并就地重建；静置 8s 挂起音频线程省电 |
| 画面 | 天空与月光用离屏层预渲染，星、流萤、声波涟漪、运动残影、暗角与颗粒逐帧叠加；高刷屏按真实 dt 步进 |
| 兼容 | `roundRect` 缺失时用 `arcTo` 补，旧内核不整页崩；竖屏/小屏自动缩短绳长与圈半径 |

## 目录结构

```
index.html              页面 + 全部样式与逻辑（单文件）
audio-data.js           真实竹知了录音的 base64 内嵌（不读文件、不发请求）
sw.js                   Service Worker：核心资源预缓存 + 导航网络优先
manifest.webmanifest    PWA 清单（可安装到桌面）
icon-192.png / icon-512.png / apple-touch-icon.png   图标
title.jpg               标题卡
2D.png / 2D_1.png       转速 0–6.5 圈/秒的两帧竹筒贴图，超过 6.5 固定用 2D_1
moon.webp               月亮素材
```

## 本地运行

Service Worker 只在安全上下文（`https://` 或 `localhost`）生效，所以别直接双击 `index.html`：

```bash
python3 -m http.server 8000
# 打开 http://localhost:8000
```

## 部署

任意静态托管都行（GitHub Pages、Cloudflare Pages、对象存储、Nginx 都适用），把仓库根目录当站点根即可。仓库内已带 `.github/workflows/pages.yml`，推 `main` 后自动发布到 GitHub Pages。

## 浏览器支持

需要 Canvas 2D 与 WebAudio。iOS Safari 13+、Android Chrome 80+ 可正常游玩；「甩手机」需要支持 DeviceMotion 的设备。首次出声必须有一次点击/触摸手势，iOS 的限制绕不过去。

## 致谢与素材

录音采样取自真实竹知了。贴图与图标为本项目自制。

## 许可

[MIT](LICENSE)
