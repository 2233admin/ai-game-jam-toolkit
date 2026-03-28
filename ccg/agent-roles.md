# Agent 角色定义

> 不同 AI 擅长不同事。知道谁干什么，才能用好 CCG。

## 角色分工

### Claude (架构师 + 代码手)
- **擅长**: 代码质量、架构设计、长文件编辑、遵循复杂指令
- **弱点**: 有时过度设计，搜索能力不如 Gemini
- **最佳用途**: 写核心逻辑、代码审查、重构
- **工具**: Claude Code CLI, Cursor (Claude 模式)

### GPT / Codex (全能实干家)
- **擅长**: 快速原型、广泛知识面、DALL-E 生图
- **弱点**: 长代码容易出错，有时"自信地犯错"
- **最佳用途**: 快速出原型、生成测试数据、头脑风暴
- **工具**: ChatGPT, Codex CLI, Cursor (GPT 模式)

### Gemini (搜索员 + 大上下文)
- **擅长**: 搜索最新信息、处理超长上下文、多模态（图片理解）
- **弱点**: 代码质量不如 Claude，有时答非所问
- **最佳用途**: 搜索文档/教程、分析截图、处理大量代码
- **工具**: Gemini CLI, Google AI Studio

## 游戏开发任务分配

| 任务 | 首选 AI | 备选 |
|------|---------|------|
| 游戏架构设计 | Claude | Gemini (搜索参考) |
| 战斗系统实现 | Claude | Codex (快速原型) |
| UI 组件开发 | Claude + 截图 | Codex |
| 搜索开源轮子 | Gemini | Codex |
| 调试 bug | Claude | 三个都试 |
| 生成美术素材 | DALL-E / Midjourney | PixelLab |
| 写对话剧本 | Claude / Codex | Gemini |
| 性能优化 | Claude | Gemini (搜索方案) |
| 部署上线 | Codex | Claude |

## 协作模式

### 模式 1: 瀑布式（简单任务）
```
Claude 写代码 → Codex 审查 → 修改 → 完成
```

### 模式 2: 竞赛式（技术选型）
```
三个 AI 各出方案 → CCG 共识门控 → 选最优 → 执行
```

### 模式 3: 流水线（复杂功能）
```
Gemini 搜索方案 → Claude 设计架构 → Claude 实现 → Codex 审查 → 完成
```

### 模式 4: 救火式（紧急 bug）
```
三个 AI 同时看 bug → 谁先给出可用修复就用谁的
```
