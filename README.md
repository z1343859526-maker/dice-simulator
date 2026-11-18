# 骰子模拟器 · MVK 骰池工具

单文件网页骰子工具，为《Marxist Vampire Killers》备团定制，也内置通用掷骰模式。
**零依赖、零构建**——整个工具就是一个 `index.html`，双击即用，离线可用。

## 在线地址（已部署）

| 入口 | 地址 | 摇一摇 | 依赖 |
|---|---|---|---|
| **GitHub Pages（推荐）** | https://z1343859526-maker.github.io/dice-simulator/ | ✅ 可用 | 无，任何设备随时访问（扫码见 `qr-github-pages.png`） |
| 局域网 | http://192.168.10.3:8123 | ❌ 不可用（HTTP 非安全上下文） | 电脑开机且运行着 `python -m http.server 8123`（扫码见 `qr-lan.png`） |

## 功能一览

### 骰池检定（MVK 规则，d20 骰池）
- **骰池数量**：自己 1 + SC 数 + KAPITAL 等级（开局默认 5 骰）
- **💰 KAPITAL 骰**：金色骰子，成功默认按 BONUS 计（可叠加，p.97）；勾选「CLASS 检定模式」后直接计入成功数
- **能力修正 / 装备修正**：加到每颗骰上，单骰 + 修正 ≥ 20 → 1 个成功（p.93）
- **需要成功数**：设定门槛后自动给出 ✅ 达成 / ❌ 未达成 结论（如创伤检定 SCARY 2 / SHOCKING 3 / HORRIFYING 4）
- **Rule of 1 and 20**（p.91）：Nat 1 标记 ☠ 必败不可转化；Nat 20 标记 ⭐ 万能成功
- **SC 抬骰提示**：未过线的骰会显示「差 N 点」，提醒可用 SC 能力抬骰（每 SC 每轮限 1 颗，p.93）
- 预设按钮：默认开局 5 骰 / CLASS 检定 / 创伤 SCARY / SHOCKING / HORRIFYING

### 通用掷骰
- 骰子表达式：`2d6+1d4+3`、`1d20`、`5-1d6` 均可（最多 60 颗，d 上限 1000）
- 快捷按钮：+1d4 ~ +1d100
- **结论判定**：总点数与目标值比较（≥ / > / ≤ / < / =），自动给出成功/失败结论
- d6 渲染为真实点数骰，其余面数渲染为数字骰

### 通用功能
- 3D 翻滚骰子动画（CSS 3D，落定回弹 + WebAudio 合成"嗒"声，可静音）
- 掷骰历史（本地保存最近 30 条）
- 参数自动记忆（localStorage）
- 随机数使用 `crypto.getRandomValues` + 拒绝采样，分布均匀无偏差

## 手机摇一摇掷骰（陀螺仪/加速度计）

点右上角「📳 摇一摇」启用后，**甩动手机即可掷骰**，甩得越狠骰子翻滚越久。
顶部有实时摇动力度条和灵敏度滑块（8=最灵敏，40=最迟钝），可按手机型号调整。

### 工作原理
- 使用浏览器标准 **DeviceMotion API**（`devicemotion` 事件，读取加速度计数据）
- 相邻两次采样加速度差值超过灵敏度阈值 → 判定为一次摇动 → 触发当前模式的掷骰
- 1.2 秒冷却防止连续误触发；Android 上附带震动反馈（`navigator.vibrate`）
- iOS 13+ Safari 需要调用 `DeviceMotionEvent.requestPermission()`（必须由用户点击触发），页面已自动处理

### ⚠ 两个硬性限制（浏览器安全策略，无法绕过）
1. **必须 HTTPS 或 localhost**：`file://` 双击打开时摇一摇不可用（页面会提示），按钮、动画、结论不受影响
2. **iOS 必须由点击授权**：首次点「摇一摇」会弹出系统权限框，选「允许」

### 如何在手机上使用（三种方式）

| 方式 | 步骤 | 适合 |
|---|---|---|
| **GitHub Pages（推荐）** | 把 `index.html` 传到任意 GitHub 仓库 → Settings → Pages → 开启 → 用手机访问 `https://<用户名>.github.io/<仓库名>/` | 长期使用、团里所有人都能访问 |
| **localhost 端口转发** | 手机 USB 连电脑，`adb reverse tcp:8123 tcp:8123`，手机访问 `http://localhost:8123` | 开发调试 |
| **本地临时服务器** | 本目录执行 `python -m http.server 8123`，电脑访问 `http://localhost:8123` | 桌面端跑团时投屏 |

注意：同一 WiFi 下用 `http://192.168.x.x:8123` 访问**不行**——非 HTTPS 的局域网地址不是安全上下文，运动传感器会被浏览器禁用。想免服务器给手机用，GitHub Pages 是最省事的方案。

## 目录内容

```
dice-simulator/
├── index.html   # 全部功能都在这一个文件里
└── README.md    # 本说明
```

## 开源参考（本项目调查过的类似项目）

如果以后想要**真物理引擎**的 3D 骰子（骰子在桌面上弹跳、碰撞），可以直接复用这些成熟项目：

- [3d-dice/dice-box](https://github.com/3d-dice/dice-box) — 最活跃的 3D 骰子模块（BabylonJS + AmmoJS，Web Worker 渲染），可作为库嵌入任何网页
- [3d-dice/dice-box-threejs](https://github.com/3d-dice/dice-box-threejs) — 同组织的 Three.js + cannon-es 版本
- [byWulf/threejs-dice](https://github.com/byWulf/threejs-dice) — three.js + cannon.js 可投掷骰子库，支持 d4~d20 定面落骰
- [MajorVictory/3DDiceRoller](https://github.com/MajorVictory/3DDiceRoller) — 经典开源 3D 骰桌（源自 Anton Natarov 的著名实现）
- 摇动检测原理参考 [MDN: DeviceMotionEvent.requestPermission()](https://developer.mozilla.org/en-US/docs/Web/API/DeviceMotionEvent/requestPermission_static)

本工具为了**零依赖、离线可用**，没有引入 three.js/物理引擎，改用 CSS 3D 变换 + 缓动曲线模拟投掷手感；如果日后想要骰子互相碰撞的物理效果，dice-box 是首选升级路径。
