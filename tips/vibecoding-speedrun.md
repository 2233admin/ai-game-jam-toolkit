# Vibecoding 比赛冲刺指南

> 24 小时内用 AI 做出一个能拿奖的游戏 demo。

## 心法

1. **Demo 优先** — 评委看的是 30 秒演示，不是你的代码。最炫的部分先做
2. **一张图 > 千字** — 别跟 AI 描述 UI 长什么样，截张图给它
3. **Fork > 从零** — 80% 的功能已经有人做过了，Fork 改皮比自己写快 10 倍
4. **能跑 > 完美** — 先让它跑起来，有时间再优化

## 时间线模板（24h）

| 时段 | 任务 | AI 用法 |
|------|------|---------|
| 0-1h | 创意确定 + Fork 模板 + 跑通 | CCG 三方头脑风暴选创意 |
| 1-3h | 核心玩法（移动+碰撞+基础交互）| Claude/Cursor 写逻辑 |
| 3-5h | 战斗/捕捉系统 | 用 `game-logic.md` 模板 |
| 5-7h | 对话系统 + NPC | 用 `game-dialogue.md` 模板 |
| 7-9h | UI 美化 | 用 `game-ui-from-ref.md` 模板 |
| 9-11h | AI 生成美术/音乐替换占位素材 | PixelLab + Suno |
| 11-12h | 测试 + 修 bug | 三个 AI 同时 debug |
| 12h+ | 录 demo 视频 + 写介绍 | AI 帮写文案 |

## 保命技巧

### 1. Git 频繁提交
```bash
# 每做完一个功能就提交
git add -A && git commit -m "feat: 战斗系统基础版"
```
AI 改坏了？`git checkout .` 回退。不提交 = 没后悔药。

### 2. 占位符先行
没有美术？用色块。没有音效？静音。没有动画？直接切。
**先让功能跑通，再换素材。**

### 3. 热更新开着
```bash
# Vite 默认热更新
npm run dev
```
改一行代码，浏览器自动刷新，省掉无数次手动刷新。

### 4. 截图驱动开发
- 找到你想要的效果的参考截图
- 贴给 AI："照这个做"
- 比你写 500 字描述管用 100 倍

### 5. 风格统一神器：CSS 变量
```css
:root {
  --bg-primary: #1a1a2e;
  --bg-panel: #16213e;
  --text-primary: #eee;
  --accent: #e94560;
  --border-radius: 8px;
  --font-game: 'Press Start 2P', monospace;
}
```
所有组件都用这些变量，风格自动统一。改一处全改。

### 6. 评委心理学
评委在每个作品上花 **不到 2 分钟**。你需要：
- **前 10 秒** 抓住眼球（炫酷的标题/开场动画）
- **30 秒内** 展示核心创意（你的游戏哪里不一样？）
- **1 分钟** 展示完整性（有开头有结尾，不是半成品）

### 7. 作弊级 Prompt
```
我在参加 Game Jam 比赛，还有 X 小时截止。
我的游戏是 [描述]，用 [技术栈]。

现在的问题是：[问题]
我不需要完美的解决方案，我需要最快能跑的方案。
给我可以直接复制粘贴的代码。
```

## 风格统一方案

**问题**：AI 生成的各个组件风格不一致，看起来像拼凑的。

**解法**：

### 1. Design Token 先行
在项目一开始就定义好设计变量：
```css
/* game-theme.css — 所有组件都引用这个文件 */
:root {
  /* 颜色 */
  --color-bg: #0f0f23;
  --color-panel: #1e1e3f;
  --color-border: #4a4a8a;
  --color-text: #ccccff;
  --color-accent: #ffcc00;
  --color-hp: #44ff44;
  --color-damage: #ff4444;

  /* 尺寸 */
  --border-width: 3px;
  --border-radius: 4px;
  --padding: 12px;
  --font-size-normal: 14px;
  --font-size-large: 18px;

  /* 像素风专用 */
  --pixel-border: 3px solid var(--color-border);
  image-rendering: pixelated;
}
```

### 2. 每次给 AI 都附上 Theme
```
使用以下设计规范（不要偏离）：
- 背景色: #0f0f23
- 面板色: #1e1e3f (带 3px #4a4a8a 边框)
- 文字色: #ccccff
- 强调色: #ffcc00
- 字体: 'Press Start 2P' 或等宽
- 圆角: 4px
- 像素风，image-rendering: pixelated
```

### 3. 组件审查 Prompt
功能做完后，统一过一遍风格：
```
检查以下代码中的所有颜色、字体、边框、间距，
确保它们全部使用 CSS 变量 var(--xxx)，
没有硬编码的颜色值或魔法数字。
如果有不一致的地方，帮我修正。
```

## AI 生图风格统一

如果用 AI 生成精灵/角色图：
```
所有图片都使用以下统一风格参数：
- 风格: pixel art, 16-bit JRPG style
- 调色板: limited palette, max 32 colors
- 背景: transparent
- 尺寸: 64x64 (精灵) / 128x128 (立绘)
- 视角: 3/4 top-down (地图) / front-facing (战斗)
```

每次生图都带上这段，风格就不会跑偏。
