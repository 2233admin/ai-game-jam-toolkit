# 推荐技术栈 — Web 游戏快速开发

> 全部开源免费，前端就能用，不需要后端。

## Tier 1: 直接 Fork 就能跑

| 项目 | 链接 | 说明 | 适合 |
|------|------|------|------|
| **Monster Tamer** | [GitHub](https://github.com/devshareacademy/monster-tamer) | Phaser 3 完整宝可梦 clone：战斗+捕捉+地图+对话 | 想做宝可梦风格，fork 改皮最快 |
| **Kaplay** | [GitHub](https://github.com/kaplayjs/kaplay) | Kaboom.js 继承者，API 极简，适合 Game Jam | 想从零搭但要快 |
| **RPG-JS** | [GitHub](https://github.com/RSamaium/RPG-JS) | TypeScript RPG 框架，PixiJS+Vue | 想做更完整的 RPG/MMO |

## Tier 2: 即插即用组件

### UI
| 项目 | 链接 | 大小 | 说明 |
|------|------|------|------|
| **RPGUI** | [GitHub](https://github.com/RonenNess/RPGUI) | 25KB | 纯 CSS RPG 风格 UI，加 class 就行 |
| **Pokemon MSG Box** | [GitHub](https://github.com/scythianwizard/old-pokemon-jrpg-style-message-box) | 5KB | 宝可梦金银对话框，打字机效果 |

### 对话
| 项目 | 链接 | 说明 |
|------|------|------|
| **inkjs** | [GitHub](https://github.com/y-lohse/inkjs) | Ink 脚本 JS 运行时，零依赖，分支对话 |
| **YarnClassic** | [GitHub](https://github.com/blurymind/YarnClassic) | 可视化对话树编辑器，导出 JSON |

### 地图
| 项目 | 链接 | 说明 |
|------|------|------|
| **Tiled** | [mapeditor.org](https://www.mapeditor.org/) | 瓦片地图编辑器，导出 JSON，Phaser/Kaplay 都支持 |
| **LDtk** | [ldtk.io](https://ldtk.io/) | 现代关卡编辑器，比 Tiled 更直觉 |

## Tier 3: AI 生成工具

| 工具 | 链接 | 用途 |
|------|------|------|
| **PixelLab** | [pixellab.ai](https://pixellab.ai) | AI 像素风角色、动画、瓦片 |
| **tldraw Make Real** | [GitHub](https://github.com/tldraw/make-real) | 手画草图 → HTML/CSS/JS |
| **Suno** | [suno.com](https://suno.com) | AI 生成背景音乐 |
| **sfxr** | [sfxr.me](https://sfxr.me/) | 8-bit 音效生成器 |

## 一键安装清单

```bash
# 方案 A: Phaser (最主流)
npm create vite@latest my-game -- --template vanilla-ts
cd my-game
npm install phaser
npm install rpgui  # 如果 npm 有的话，否则直接下载 CSS/JS

# 方案 B: Kaplay (最简单)
npx create-kaplay my-game
cd my-game

# 方案 C: RPG-JS (最完整)
npx degit RSamaium/RPG-JS/starter my-game
cd my-game
npm install
```

## 免费素材库

| 网站 | 链接 | 说明 |
|------|------|------|
| **OpenGameArt** | [opengameart.org](https://opengameart.org) | 免费游戏素材，CC 协议 |
| **itch.io Assets** | [itch.io/game-assets](https://itch.io/game-assets) | 大量免费/付费素材包 |
| **Kenney** | [kenney.nl](https://kenney.nl) | CC0 协议素材，商用无忧 |
| **Pokemon-style Tileset** | 搜 OpenGameArt | 有不少宝可梦风格地图块 |
