# LaneSpeed 限速编辑器

**Lanelet2 / Autoware 矢量地图的限速修改工具。**
不用安装、不用联网、不用任何运行环境——**下载一个文件，双击就能用。**

> 本仓库只放**可以直接使用的成品文件**，不含源代码。
> 使用时全部计算都在你自己的电脑上完成，地图不会上传到任何地方。

## 怎么拿到这个文件（任选一种，都不需要账号）

**方式 1 · 浏览器直接下载（最直观）**
打开这个仓库 → 点上面的 **`LaneSpeed.html`** → 右上角 **Download raw file**。
下载完双击就能用。

**方式 2 · 一条命令（不需要 git、不需要登录）**

```sh
curl -L -o LaneSpeed.html https://cdn.jsdelivr.net/gh/lmy789533/LaneSpeed@main/LaneSpeed.html
```

**方式 3 · GitHub 直链**（浏览器地址栏粘贴即可下载）

```
https://github.com/lmy789533/LaneSpeed/raw/main/LaneSpeed.html
```

**方式 4 · 下载整个仓库的 zip**（不要 git）

```
https://codeload.github.com/lmy789533/LaneSpeed/zip/refs/heads/main
```

**方式 5 · 最省事**：这个文件只有 **70 KB**，直接把 `LaneSpeed.html` 用微信 / 邮件 / U 盘发给对方就行。
它不依赖任何其他文件，单独一个文件就能跑。

**方式 6 · 用 git 拉取**（需要本机能正常访问 github.com）

```sh
git clone --depth 1 https://github.com/lmy789533/LaneSpeed.git
# 然后双击 LaneSpeed/LaneSpeed.html
```

### 为什么 `git clone` 会要账号密码？

这个仓库是**公开**的，匿名克隆本来不需要任何账号。如果被要求输入账号密码，是**本机网络或凭据环境**的问题，不是仓库设了权限。常见原因和解决办法：

1. **网络中间设备拦截**：有些公司网络 / 代理会对 github.com 返回 401，git 收到后就弹出登录框。
   先确认真实报错（不弹框）：

   ```sh
   GIT_TERMINAL_PROMPT=0 git clone --depth 1 https://github.com/lmy789533/LaneSpeed.git
   ```

   如果报 `Couldn't connect` / `HTTP2 framing layer` / `Empty reply from server`，就是网络不通，
   **请直接用上面的方式 1–5**，它们不需要 git。

2. **本机存了旧的 GitHub 账号密码**（换过账号、改过密码后最常见）。
   macOS 清除：

   ```sh
   printf "protocol=https\nhost=github.com\n\n" | git credential-osxkeychain erase
   ```

   Windows：控制面板 → 凭据管理器 → Windows 凭据 → 删除 `git:https://github.com`。

3. **git 版本或代理问题**，可以试：

   ```sh
   git -c http.version=HTTP/1.1 -c http.lowSpeedLimit=0 -c http.lowSpeedTime=999999 clone --depth 1 \
     https://github.com/lmy789533/LaneSpeed.git
   ```

4. **就是访问不了 github.com**：用方式 2 的 CDN 地址（`cdn.jsdelivr.net` 在国内一般可直连），
   或者让别人把这个 70 KB 的文件直接发给你。

## 怎么用（30 秒）

1. 用上面的方式拿到 **`LaneSpeed.html`**（只有一个文件）。
2. **双击它**，用浏览器打开（Chrome / Edge 最好，Firefox、Safari 也可以）。
3. 点「打开地图…」选你的 `lanelet2_map.osm`（也可以直接把文件拖进窗口）。
4. 左侧填速度 → 点「应用」→ 点「导出副本…」另存为新文件。

**原始地图永远不会被覆盖**：程序只读原文件，导出时必须另存为新文件。
保存时如果用 Chrome/Edge，会弹出「另存为」让你选目录；用其他浏览器则会落到"下载"文件夹。

## 这个工具能做什么

- 按 **直行 / 左转 / 右转** 分组批量改限速（依据 `turn_direction`）。
- 地图上直接看到**当前限速**：改过的车道标黄，放大后显示黄色数字。
- **行驶方向箭头**：箭头就是车头方向。
- **方向分层**：单车道双向行驶的地图会自动分成**正向层 / 反向层**（判据是"左边界应在行驶方向左侧"）。
  可以只看某一层、两层错开显示，可以**分图层改速**（正向一套速度、反向一套速度），
  还能把反向层的数据导出成 CSV。
- 「查看修改结果」只显示改过的车道并放大到数值看得清；「逐条核对」按顺序一条条放大，
  适合一次改了几千条的情况。
- 缩到整张地图时自动改用线条绘制、放大时自动降低填充透明度，路口重叠也看得清。
- 米制网格与比例尺、坐标指示、悬停车道信息、车道列表（可筛选/搜索）。

## 安全边界（重要）

- **只改 `speed_limit`**：节点、边界、ID、转向属性和其他标签一律不动；导出文件除速度值外与原文件**逐字节相同**。
- 导出前会重新解析并逐条校验；禁止覆盖本次导入的原文件。
- **全程离线**：没有任何网络请求（你可以断网使用）。
- 不动交通标志、限速监管元素、`speed_limit:vehicle` 等参与者专属限速。
- 上线前请在 Autoware 测试环境和实车流程中验证。**本工具的读写测试不等于实车安全验证。**

## 常见问题

**改了速度，地图上看不到数字？**
数字只在车道画得足够大时显示。先看黄色标记定位，再放大，或直接点「查看修改结果」。

**路网叠在一起看不清？**
放大后路口本来就会互相重叠。左侧「地图显示」里关掉「车道填充颜色」只看轮廓最清楚。

**浏览器提示"无法打开文件"？**
请用 `LaneSpeed.html`（单文件版）而不是解压出来的 `index.html`；或者换 Chrome / Edge。

**能改多大的地图？**
支持 150 MB 以内的 `.osm`。更大的建议先用地图工具拆分。

## 许可

专有软件：**可免费使用（含商业项目）**，禁止再分发、修改，或用于提供收费服务。
详见 [LICENSE](LICENSE)。Copyright (c) 2026. All rights reserved.

## 更新记录

**2.0.1**
- 地图解析更严格：拦截非法 XML 字符、非法实体引用、属性语法错误、根元素之外的杂内容，
  以及重复属性；异常地图会被明确拒绝，而不是带着问题读进来。
- 导出速度恢复（此前一轮加固让解析/导出变慢约 2 倍，已优化回来）。
- 桌面版：导出时若目标指向刚导入的原图（含软链接）会直接拒绝，并改用"临时文件 + 原子替换"
  写入，避免写坏文件。

**2.0.0**
- 首个跨平台版本：单文件网页版，Windows / Linux / macOS 通用，离线运行。
- 方向分层（正向层 / 反向层）、分层改速、反向层数据导出 CSV。
- 限速数值直接标在地图上、方向箭头按实际行驶方向绘制。
