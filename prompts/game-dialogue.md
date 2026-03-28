# Game Dialogue 对话系统生成模板

> 快速搭建 RPG 对话系统，支持分支剧情、表情切换、打字机效果

## 方案一：用 inkjs（推荐，最快）

### 安装
```bash
npm install inkjs
```

### Ink 脚本示例（`story.ink`）
```ink
=== start ===
老橡树博士站在实验室中央，桌上摆着三个精灵球。

博士: 欢迎来到精灵世界！我是橡树博士。
博士: 今天是你踏上冒险旅途的日子。

* [选火系精灵] -> choose_fire
* [选水系精灵] -> choose_water
* [选草系精灵] -> choose_grass

=== choose_fire ===
博士: 小火龙！勇敢的选择。
博士: 火系精灵攻击力强，但要小心水系对手。
~ chosen = "fire"
-> receive_pokemon

=== choose_water ===
博士: 杰尼龟！稳健的选择。
博士: 水系精灵防御均衡，适合新手。
~ chosen = "water"
-> receive_pokemon

=== choose_grass ===
博士: 妙蛙种子！有远见的选择。
博士: 草系精灵前期稍弱，但进化后非常强力。
~ chosen = "grass"
-> receive_pokemon

=== receive_pokemon ===
你获得了你的第一只精灵！
博士: 带上这个图鉴，去探索这个世界吧。
-> END
```

### 在游戏中调用
```typescript
import { Story } from 'inkjs'

// 加载编译后的 ink JSON
const story = new Story(inkJsonContent)

function advance() {
  // 获取下一段文本
  while (story.canContinue) {
    const text = story.Continue()
    displayDialogue(text) // 你的打字机渲染函数
  }

  // 如果有选项
  if (story.currentChoices.length > 0) {
    showChoices(story.currentChoices.map(c => c.text))
  }
}

function choose(index: number) {
  story.ChooseChoiceIndex(index)
  advance()
}
```

---

## 方案二：纯 JSON 驱动（不想引入依赖时）

### 对话数据格式
```json
{
  "npc_oak_001": {
    "speaker": "橡树博士",
    "portrait": "oak",
    "lines": [
      {
        "text": "欢迎来到精灵世界！",
        "emotion": "smile",
        "effects": []
      },
      {
        "text": "这里既{red}危险{/red}又{shake}刺激{/shake}...",
        "emotion": "serious",
        "effects": ["screen_dim"]
      },
      {
        "text": "你准备好了吗？",
        "emotion": "smile",
        "choices": [
          { "text": "准备好了！", "next": "npc_oak_ready", "flag": "player_ready" },
          { "text": "再等等...", "next": "npc_oak_wait" }
        ]
      }
    ]
  }
}
```

### 让 AI 帮你生成对话

```
根据以下剧情大纲，生成 JSON 格式的 NPC 对话数据：

剧情：玩家第一次进入蒙德草原，遇到一个受伤的旅行者。旅行者警告玩家前方有
强力野生精灵出没，并教玩家如何使用精灵球捕捉。对话结束后给玩家 5 个精灵球。

要求：
- 用上面的 JSON 格式
- 3-5 轮对话
- 至少一个选项分支
- 包含表情切换（pain → grateful → serious）
- 对话结束后触发道具获取事件
```

---

## 方案三：AI 实时生成对话（高级玩法）

```
你是一个 RPG 游戏中的 NPC。根据以下设定实时生成对话。

NPC 设定：
- 名字：{npc_name}
- 性格：{personality}
- 位置：{location}
- 当前剧情阶段：{plot_stage}

玩家状态：
- 队伍精灵：{player_team}
- 已完成任务：{completed_quests}
- 好感度：{affection_level}

规则：
1. 每次回复 1-2 句话，不要长篇大论
2. 根据好感度调整语气（低=冷淡，高=亲切）
3. 如果玩家队伍很强，表示佩服；如果很弱，给建议
4. 回复格式：
{
  "text": "对话内容",
  "emotion": "表情",
  "choices": [可选的回复选项],
  "trigger": "可选的事件触发"
}
```

---

## 打字机效果实现（复制即用）

```typescript
class TypeWriter {
  private element: HTMLElement
  private speed: number
  private queue: string[] = []
  private typing = false

  constructor(element: HTMLElement, speed = 50) {
    this.element = element
    this.speed = speed
  }

  async show(text: string): Promise<void> {
    this.typing = true
    this.element.textContent = ''

    for (let i = 0; i < text.length; i++) {
      if (!this.typing) {
        // 点击跳过 — 直接显示全文
        this.element.textContent = text
        return
      }
      this.element.textContent += text[i]
      await this.delay(this.speed)
    }
    this.typing = false
  }

  skip() {
    this.typing = false
  }

  private delay(ms: number) {
    return new Promise(resolve => setTimeout(resolve, ms))
  }
}

// 使用
const tw = new TypeWriter(document.getElementById('dialogue-text')!, 40)
document.addEventListener('click', () => tw.skip())
await tw.show('欢迎来到精灵世界！')
```

## 富文本标记解析（可选）

```typescript
// 支持 {red}文字{/red}、{shake}文字{/shake}、{big}文字{/big}
function parseRichText(text: string): HTMLElement[] {
  const regex = /\{(\w+)\}(.*?)\{\/\1\}/g
  const container = document.createElement('span')
  let lastIndex = 0
  let match

  while ((match = regex.exec(text)) !== null) {
    // 普通文本
    if (match.index > lastIndex) {
      container.appendChild(document.createTextNode(text.slice(lastIndex, match.index)))
    }
    // 带效果的文本
    const span = document.createElement('span')
    span.className = `effect-${match[1]}`
    span.textContent = match[2]
    container.appendChild(span)
    lastIndex = regex.lastIndex
  }

  // 剩余文本
  if (lastIndex < text.length) {
    container.appendChild(document.createTextNode(text.slice(lastIndex)))
  }

  return [container]
}
```

对应 CSS：
```css
.effect-red { color: #ff4444; }
.effect-shake { animation: shake 0.3s infinite; }
.effect-big { font-size: 1.5em; font-weight: bold; }

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-2px); }
  75% { transform: translateX(2px); }
}
```
