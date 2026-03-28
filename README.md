# AI Game Jam Toolkit 🎮

> Vibecoding 比赛武器库 — 用 AI 在一天内做出一个能玩的游戏

专为 Web 前端开发者设计。适用于宝可梦/RPG 风格页游快速原型开发。

## 速通路线（30 分钟上手）

```
1. Fork monster-tamer 或 kaplay 模板 → 已有战斗+地图+对话
2. PixelLab / Banana Pro 批量生成精灵 sprite sheet → 替换素材
3. RPGUI 套上菜单/背包 UI → 加 CSS class 就行
4. inkjs 写 NPC 对话脚本 → 分支剧情
5. Claude / Cursor vibe code 剩下的功能
```

## 目录结构

```
ai-game-jam-toolkit/
├── README.md                          # 你在看的这个
├── prompts/
│   ├── game-ui-from-ref.md            # 截图 → UI 组件（核心模板）
│   ├── game-logic.md                  # 战斗/捕捉/进化逻辑生成
│   ├── game-dialogue.md               # 对话系统 + 剧情分支
│   └── vibe-workflow.md               # Vibecoding 工作流（怎么跟 AI 协作）
├── ccg/
│   ├── README.md                      # CCG 多 Agent 协同系统说明
│   ├── consensus-gate.md              # 共识门控规则
│   ├── agent-roles.md                 # Agent 角色定义
│   └── workflow-examples.md           # 实战示例
├── starters/
│   └── recommended-stack.md           # 推荐技术栈 + 开源项目清单
└── tips/
    └── vibecoding-speedrun.md         # 比赛冲刺技巧
```

## 核心武器

### 1. Prompt 模板 (`prompts/`)
给 AI 一张截图或一段描述，直接生成可用的游戏组件代码。不用跟 AI 废话解释 UI 长什么样。

### 2. CCG 多 Agent 协同 (`ccg/`)
Codex + Claude + Gemini 三方共识系统。让三个 AI 互相审查、互相补充，一个人顶三个人。

### 3. 推荐技术栈 (`starters/`)
精选的开源项目，全部可以直接 fork 或 npm install。

### 4. 比赛技巧 (`tips/`)
从实战中总结的 vibecoding 方法论。

## 推荐工具速查

| 类别 | 工具 | 说明 |
|------|------|------|
| 游戏引擎 | [Phaser 3](https://github.com/phaserjs/phaser) (37k⭐) | 主流 2D HTML5 游戏框架 |
| 宝可梦模板 | [Monster Tamer](https://github.com/devshareacademy/monster-tamer) | Phaser 3 完整宝可梦 clone |
| 轻量引擎 | [Kaplay](https://github.com/kaplayjs/kaplay) (1.4k⭐) | Kaboom.js 继承者，API 极简 |
| RPG 框架 | [RPG-JS](https://github.com/RSamaium/RPG-JS) (1.5k⭐) | TS RPG/MMO 框架 |
| RPG UI | [RPGUI](https://github.com/RonenNess/RPGUI) (930⭐) | 纯 CSS RPG 风格 UI，25KB |
| 对话框 | [Pokemon MSG Box](https://github.com/scythianwizard/old-pokemon-jrpg-style-message-box) | 宝可梦金银风格打字机对话 |
| 对话引擎 | [inkjs](https://github.com/y-lohse/inkjs) | Ink 脚本 JS 版，零依赖 |
| 对话编辑 | [YarnClassic](https://github.com/blurymind/YarnClassic) (300⭐) | 可视化对话树编辑器 |
| AI 像素画 | [PixelLab](https://pixellab.ai) | AI 生成角色/动画/地图块 |
| 线框→代码 | [tldraw Make Real](https://github.com/tldraw/make-real) (5k⭐) | 手画 UI → 可用代码 |
| 无代码引擎 | [GDevelop](https://github.com/4ian/GDevelop) (21.6k⭐) | 内置 AI Agent |

## 适用场景

- Vibecoding 比赛 / Game Jam
- AI 驱动的游戏原型开发
- 个人独立游戏快速验证
- 学习 AI 辅助编程

## License

MIT — 随便用，赢了请吃饭就行。
