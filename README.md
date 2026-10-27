# Pulse 音乐播放器 · 高保真交互原型

一款面向手机端音乐播放器 App 的**可交互高保真原型**，对应 PRD《音乐播放器 App 产品需求》。
纯 HTML/CSS/JS 实现（零依赖、零构建），附带可直接安装的 Android Demo APK（WebView 壳）。

**功能覆盖**：本地音乐库（歌曲/歌手/专辑/文件夹 + 聚合搜索 + 排序）、沉浸式播放页（动态取色 + 逐字歌词）、
播放队列管理、歌单与收藏闭环、最近播放、深浅色主题、锁屏播放演示、骨架屏/空态/错误态。

![页面预览](#-页面一览) · 许可证 [MIT](LICENSE)

---

## 快速开始

**方式一：浏览器直接打开**（推荐评审用）

下载 [prototype.html](prototype.html)，双击用 Chrome / Edge / Safari 打开即可，**离线可用**（图标库已内嵌）。

**方式二：安装 Android APK**（真机走查用）

下载 [pulse-music-prototype.apk](pulse-music-prototype.apk) 传到手机安装（需 Android 7.0+），
桌面名「Pulse 音乐原型」，安装后完全离线运行。

> 两种方式体验完全一致；APK 为 WebView 壳 + 内嵌原型页，debug 签名仅供演示，不可上架。

## 核心交互

| 操作 | 方式 |
|---|---|
| 展开沉浸式播放页 | 点击底部毛玻璃迷你播放器 |
| 切歌 | 播放页左右滑动封面 |
| 进入歌词页 | 播放页轻点封面（逐字卡拉OK高亮，点击歌词行跳转进度） |
| 播放队列 | 播放页右上角按钮 / 上滑封面，支持移除歌曲 |
| 收藏闭环 | 播放页 ❤ ↔「我喜欢的音乐」歌单实时三处联动 |
| 歌单管理 | 新建（命名+封面风格）/ 重命名 / 删除（红色确认） |
| 聚合搜索 | 音乐库搜索框，歌曲/歌手/专辑/歌单分组结果 |
| 深浅色切换 | 设置页「深色模式」 |
| 锁屏播放演示 | 页面右侧「快速跳转 → 锁屏演示」 |
| 设计调参 | 卡片圆角 / 毛玻璃强度 / 主题色 / 动态取色 实时可调 |

## 设计 Token（可直接交给工程）

| Token | 值 | 说明 |
|---|---|---|
| 圆角 | 22px（卡片）/ 12px（小元素） | 统一圆角体系，原型内可实时调参 |
| 毛玻璃 | blur 24px + saturate 1.5 | 强度受控，低端机可降级为半透明纯色 |
| 主题色 | `#7C6CFF`（深色）/ `#6A58F5`（浅色） | 播放页色彩由封面动态取色生成 |
| 图标 | Lucide 线性图标，统一 1.8 线宽 | |
| 字体 | 系统字体栈（PingFang SC / Microsoft YaHei） | |
| 动效 | 0.3–0.5s，`cubic-bezier(.32,.72,.22,1)` | 尊重系统「减弱动态效果」设置 |

## Android 壳工程（android/）

最小 WebView 壳，把 `prototype.html` 作为离线资源装载：

```
android/app/
├── AndroidManifest.xml          # minSdk 24 / targetSdk 34，全屏 NoActionBar 主题
├── src/com/pulse/prototype/
│   └── MainActivity.java        # WebView（JS+DOM Storage，返回键处理）
└── res/mipmap-xxxhdpi/          # 启动图标
assets/index.html                # 即 prototype.html，打包时复制进去
```

自行构建需要 `aapt2 / d8 / zipalign / apksigner`（build-tools 34+）与 `android.jar`（platform 34），流程：

```bash
aapt2 compile --dir app/res -o compiled.flata
aapt2 link -o base.apk -I $SDK/platforms/android-34/android.jar \
    --manifest app/AndroidManifest.xml -A assets \
    --min-sdk-version 24 --target-sdk-version 34 compiled.flata
javac --release 8 -cp $SDK/platforms/android-34/android.jar \
    -d classes app/src/com/pulse/prototype/MainActivity.java
d8 --release --lib $SDK/platforms/android-34/android.jar \
    --output dex classes/com/pulse/prototype/MainActivity.class
# 把 dex/classes.dex 加入 base.apk，然后：
zipalign -f -p 4 base.apk aligned.apk
apksigner sign --ks <your.jks> --out app.apk aligned.apk
```

## 目录结构

```
├── prototype.html               # 全部原型（单文件，离线可用）
├── pulse-music-prototype.apk    # Android 安装包
├── android/                     # WebView 壳工程源码
├── 使用说明.md                   # 面向评审的中文说明
└── LICENSE                      # MIT
```

## 已知边界

- 音频为模拟播放（定时器驱动进度），未接真实解码；数据为 8 首虚构歌曲 + 虚构歌词
- 队列拖拽排序以把手图标示意，未实现真实拖拽
- 均衡器为「即将推出」占位；逐字歌词效果已预览

## 许可证

[MIT](LICENSE) © 2026 cjy-kenny
