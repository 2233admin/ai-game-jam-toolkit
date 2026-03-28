# CCG — 多 Agent 共识协同系统

> Codex + Claude + Gemini 三方共识门控。让三个 AI 互审，一个人顶三个。

## 什么是 CCG？

CCG (Consensus Gate) 是一套多 AI 协同工作流：
1. 同一个问题/任务，分别发给 2-3 个 AI
2. 收集它们的独立回答
3. 用共识规则合并结论，取长补短

**为什么有用：** 单个 AI 有盲区。Claude 擅长代码架构，Gemini 擅长搜索和大上下文，Codex 擅长快速实现。三个一起用，覆盖面更广，错误率更低。

## 共识分类

| 分类 | 条件 | 行动 |
|------|------|------|
| ✅ **AGREE** | 三方方向一致 | 直接执行 |
| ⚠️ **SPLIT** | 方向一致但细节分歧 | 选一个最佳方案，给理由 |
| ❌ **CONFLICT** | 根本方向冲突 | 暂停，人工决策 |

## 快速上手（5 分钟）

### 方式一：手动三窗口法（最简单）

1. 打开三个 AI 窗口（Claude + ChatGPT/Codex + Gemini）
2. 同一个问题复制粘贴发给三个
3. 对照回答，用下面的模板合并

### 方式二：用 Claude Code 自动化

```bash
# 安装 Claude Code
npm install -g @anthropic-ai/claude-code

# 在项目目录下使用
cd your-game-project
claude
```

在 Claude Code 里可以调用 CCG 工作流：
- Claude 直接回答
- 调 Codex CLI 获取第二意见
- 调 Gemini CLI 获取第三意见
- 自动合并

### 方式三：在 Cursor 里用 Rules

把 `consensus-gate.md` 的规则放到 Cursor 的 `.cursorrules` 文件里，
让 Cursor 在生成代码时自动考虑多方案对比。

## 游戏开发实战示例

详见 [workflow-examples.md](workflow-examples.md)

## 文件说明

| 文件 | 说明 |
|------|------|
| `consensus-gate.md` | 共识门控核心规则 |
| `agent-roles.md` | 各 Agent 角色定义和分工 |
| `workflow-examples.md` | 游戏开发场景的实战示例 |
