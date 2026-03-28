# 视觉小说对话系统 — 明日方舟风格

> 两个立绘对对碰 + 伪 L2D 呼吸动画 + 选项分支。不用 Live2D SDK，纯 CSS/JS 实现。

## 效果描述

```
┌──────────────────────────────────────────────┐
│                   [背景图]                     │
│                                              │
│   ┌─────┐                        ┌─────┐    │
│   │ 左方 │                        │ 右方 │    │
│   │ 立绘 │     (微微呼吸晃动)      │ 立绘 │    │
│   │(说话)│                        │(暗淡)│    │
│   └──┬──┘                        └──┬──┘    │
│      │                              │        │
│ ┌────┴──────────────────────────────┴────┐   │
│ │  [阿米娅]                               │   │
│ │  博士，前方侦测到敌方活动，我们需要...▼   │   │
│ │                                        │   │
│ │  ► 立刻出击                             │   │
│ │  ► 等待增援                             │   │
│ │  ► 撤退                                │   │
│ └────────────────────────────────────────┘   │
└──────────────────────────────────────────────┘
```

## 核心特性

1. **双立绘对峙** — 左右各一个角色，说话方高亮+前移，另一方暗淡后退
2. **伪 L2D 动画** — 不用 Live2D SDK，用 CSS 分层实现呼吸/晃动/眨眼
3. **打字机对话** — 逐字显示，点击加速/跳过
4. **选项分支** — 对话结束后弹出选项，选择走不同剧情线
5. **表情切换** — 同一角色不同表情无缝切换

---

## 完整实现（复制即用）

下面是一个独立可运行的 HTML 文件，直接浏览器打开就能看效果：

```html
<!DOCTYPE html>
<html lang="zh">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Visual Novel Dialogue</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }

/* ===== 舞台 ===== */
.stage {
  position: relative;
  width: 100vw;
  height: 100vh;
  background: #1a1a2e;
  overflow: hidden;
  font-family: 'Microsoft YaHei', 'PingFang SC', sans-serif;
}

/* 背景 */
.stage-bg {
  position: absolute;
  inset: 0;
  background: linear-gradient(180deg, #0f0f23 0%, #1a1a3e 50%, #2a1a3e 100%);
  z-index: 0;
}

/* ===== 立绘容器 ===== */
.character {
  position: absolute;
  bottom: 180px;
  width: 400px;
  height: 600px;
  z-index: 1;
  transition: all 0.4s ease;
  /* 伪 L2D：呼吸动画 */
  animation: breathing 4s ease-in-out infinite;
}

.character.left {
  left: 5%;
  transform-origin: bottom center;
}

.character.right {
  right: 5%;
  transform-origin: bottom center;
  animation-delay: -2s; /* 错开呼吸节奏 */
}

/* 说话状态：高亮 + 前移 */
.character.speaking {
  filter: brightness(1.0);
  z-index: 2;
}
.character.speaking.left { left: 8%; }
.character.speaking.right { right: 8%; }

/* 沉默状态：暗淡 + 后退 + 缩小 */
.character.silent {
  filter: brightness(0.4) saturate(0.5);
  transform: scale(0.95);
  z-index: 1;
}
.character.silent.left { left: 2%; }
.character.silent.right { right: 2%; }

/* ===== 伪 L2D 分层 ===== */
.char-layer {
  position: absolute;
  inset: 0;
  background-size: contain;
  background-repeat: no-repeat;
  background-position: bottom center;
}

/* 身体层 — 呼吸 */
.char-body {
  animation: bodyBreath 4s ease-in-out infinite;
}

/* 头发层 — 轻微晃动 */
.char-hair {
  animation: hairSway 5s ease-in-out infinite;
}

/* 表情层 — 眨眼 */
.char-face {
  animation: blink 4s ease-in-out infinite;
}

/* ===== 呼吸动画 ===== */
@keyframes breathing {
  0%, 100% { transform: translateY(0) scale(1); }
  50% { transform: translateY(-3px) scale(1.005); }
}

@keyframes bodyBreath {
  0%, 100% { transform: scaleY(1) translateY(0); }
  50% { transform: scaleY(1.003) translateY(-2px); }
}

@keyframes hairSway {
  0%, 100% { transform: rotate(0deg) translateX(0); }
  25% { transform: rotate(0.3deg) translateX(1px); }
  75% { transform: rotate(-0.3deg) translateX(-1px); }
}

@keyframes blink {
  0%, 45%, 55%, 100% { transform: scaleY(1); }
  50% { transform: scaleY(0.1); } /* 眨眼 */
}

/* ===== 对话框 ===== */
.dialogue-box {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  height: 180px;
  background: linear-gradient(180deg, rgba(0,0,0,0) 0%, rgba(0,0,0,0.85) 20%);
  z-index: 10;
  padding: 30px 40px 20px;
  cursor: pointer;
}

.speaker-name {
  font-size: 16px;
  font-weight: bold;
  color: #ffcc00;
  margin-bottom: 8px;
  letter-spacing: 2px;
  text-shadow: 0 0 10px rgba(255,204,0,0.5);
}

.dialogue-text {
  font-size: 18px;
  color: #eee;
  line-height: 1.6;
  min-height: 60px;
}

/* 翻页提示 */
.next-indicator {
  position: absolute;
  bottom: 15px;
  right: 40px;
  color: #fff;
  font-size: 14px;
  animation: pulse 1s ease-in-out infinite;
  opacity: 0;
}
.next-indicator.visible { opacity: 1; }

@keyframes pulse {
  0%, 100% { opacity: 0.4; transform: translateY(0); }
  50% { opacity: 1; transform: translateY(3px); }
}

/* ===== 选项 ===== */
.choices-container {
  position: absolute;
  bottom: 200px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 20;
  display: flex;
  flex-direction: column;
  gap: 10px;
  width: 500px;
}

.choice-btn {
  background: rgba(0,0,0,0.7);
  border: 1px solid rgba(255,204,0,0.3);
  color: #eee;
  padding: 12px 20px;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.2s;
  font-family: inherit;
  text-align: left;
}

.choice-btn:hover {
  background: rgba(255,204,0,0.15);
  border-color: #ffcc00;
  color: #ffcc00;
  transform: translateX(5px);
}

.choice-btn::before {
  content: '► ';
  opacity: 0;
  transition: opacity 0.2s;
}
.choice-btn:hover::before {
  opacity: 1;
}

/* ===== 角色切换动画 ===== */
.character.entering-left {
  animation: slideInLeft 0.5s ease forwards;
}
.character.entering-right {
  animation: slideInRight 0.5s ease forwards;
}
.character.exiting-left {
  animation: slideOutLeft 0.5s ease forwards;
}
.character.exiting-right {
  animation: slideOutRight 0.5s ease forwards;
}

@keyframes slideInLeft {
  from { transform: translateX(-100%); opacity: 0; }
  to { transform: translateX(0); opacity: 1; }
}
@keyframes slideInRight {
  from { transform: translateX(100%); opacity: 0; }
  to { transform: translateX(0); opacity: 1; }
}
@keyframes slideOutLeft {
  from { transform: translateX(0); opacity: 1; }
  to { transform: translateX(-100%); opacity: 0; }
}
@keyframes slideOutRight {
  from { transform: translateX(0); opacity: 1; }
  to { transform: translateX(100%); opacity: 0; }
}

/* ===== 用纯色占位的 demo 立绘 ===== */
.demo-portrait {
  width: 100%;
  height: 100%;
  border-radius: 8px;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  padding-bottom: 20px;
  font-size: 24px;
  color: rgba(255,255,255,0.3);
  font-weight: bold;
}
.character.left .demo-portrait {
  background: linear-gradient(180deg, transparent 0%, rgba(100,150,255,0.3) 100%);
}
.character.right .demo-portrait {
  background: linear-gradient(180deg, transparent 0%, rgba(255,100,150,0.3) 100%);
}
</style>
</head>
<body>

<div class="stage">
  <div class="stage-bg"></div>

  <!-- 左方角色 -->
  <div id="charLeft" class="character left speaking">
    <!-- 实际项目中替换为分层立绘图片 -->
    <div class="demo-portrait">角色 A</div>
    <!-- 分层示例（用真实图片时取消注释）:
    <div class="char-layer char-body" style="background-image: url('char_a_body.png')"></div>
    <div class="char-layer char-hair" style="background-image: url('char_a_hair.png')"></div>
    <div class="char-layer char-face" style="background-image: url('char_a_face.png')"></div>
    -->
  </div>

  <!-- 右方角色 -->
  <div id="charRight" class="character right silent">
    <div class="demo-portrait">角色 B</div>
  </div>

  <!-- 对话框 -->
  <div id="dialogueBox" class="dialogue-box">
    <div id="speakerName" class="speaker-name"></div>
    <div id="dialogueText" class="dialogue-text"></div>
    <div id="nextIndicator" class="next-indicator">▼ 点击继续</div>
  </div>

  <!-- 选项容器 -->
  <div id="choicesContainer" class="choices-container" style="display:none"></div>
</div>

<script>
// ===== 对话引擎 =====

class DialogueEngine {
  constructor() {
    this.speakerName = document.getElementById('speakerName')
    this.dialogueText = document.getElementById('dialogueText')
    this.nextIndicator = document.getElementById('nextIndicator')
    this.choicesContainer = document.getElementById('choicesContainer')
    this.charLeft = document.getElementById('charLeft')
    this.charRight = document.getElementById('charRight')
    this.dialogueBox = document.getElementById('dialogueBox')

    this.currentScript = null
    this.currentLine = 0
    this.typing = false
    this.typeSpeed = 40 // ms per character

    this.dialogueBox.addEventListener('click', () => this.advance())
  }

  // 加载剧本
  loadScript(script) {
    this.currentScript = script
    this.currentLine = 0
    this.showLine()
  }

  // 显示当前行
  showLine() {
    if (!this.currentScript || this.currentLine >= this.currentScript.length) return

    const line = this.currentScript[this.currentLine]

    // 切换说话角色高亮
    if (line.speaker === 'left' || line.position === 'left') {
      this.charLeft.className = 'character left speaking'
      this.charRight.className = 'character right silent'
    } else if (line.speaker === 'right' || line.position === 'right') {
      this.charLeft.className = 'character left silent'
      this.charRight.className = 'character right speaking'
    }

    // 显示名字
    this.speakerName.textContent = line.name || ''

    // 打字机效果
    this.typeText(line.text, () => {
      if (line.choices) {
        this.showChoices(line.choices)
      } else {
        this.nextIndicator.classList.add('visible')
      }
    })
  }

  // 打字机
  async typeText(text, onComplete) {
    this.typing = true
    this.dialogueText.textContent = ''
    this.nextIndicator.classList.remove('visible')
    this.choicesContainer.style.display = 'none'

    for (let i = 0; i < text.length; i++) {
      if (!this.typing) {
        this.dialogueText.textContent = text
        break
      }
      this.dialogueText.textContent += text[i]
      await new Promise(r => setTimeout(r, this.typeSpeed))
    }

    this.typing = false
    if (onComplete) onComplete()
  }

  // 显示选项
  showChoices(choices) {
    this.choicesContainer.innerHTML = ''
    this.choicesContainer.style.display = 'flex'

    choices.forEach((choice, i) => {
      const btn = document.createElement('button')
      btn.className = 'choice-btn'
      btn.textContent = choice.text
      btn.style.animationDelay = `${i * 0.1}s`
      btn.addEventListener('click', (e) => {
        e.stopPropagation()
        this.choicesContainer.style.display = 'none'
        if (choice.next) {
          this.loadScript(choice.next)
        } else {
          this.currentLine++
          this.showLine()
        }
      })
      this.choicesContainer.appendChild(btn)
    })
  }

  // 点击推进
  advance() {
    if (this.typing) {
      this.typing = false
      return
    }

    const line = this.currentScript[this.currentLine]
    if (line && line.choices) return // 有选项时不推进

    this.currentLine++
    if (this.currentLine < this.currentScript.length) {
      this.showLine()
    } else {
      this.dialogueText.textContent = '— 剧情结束 —'
      this.speakerName.textContent = ''
      this.nextIndicator.classList.remove('visible')
    }
  }
}

// ===== Demo 剧本 =====

const branchReady = [
  { position: 'left', name: '阿米娅', text: '好的博士！全队准备就绪，立刻出发！' },
  { position: 'right', name: '凯尔希', text: '……鲁莽。但我会配合。' },
  { position: 'left', name: '阿米娅', text: '作战开始——全员，前进！' },
]

const branchWait = [
  { position: 'right', name: '凯尔希', text: '明智的判断。增援部队预计 10 分钟后抵达。' },
  { position: 'left', name: '阿米娅', text: '博士总是这么谨慎呢。' },
  { position: 'right', name: '凯尔希', text: '谨慎是活到最后的必要条件。' },
]

const branchRetreat = [
  { position: 'right', name: '凯尔希', text: '撤退并不可耻。保存有生力量是指挥官的职责。' },
  { position: 'left', name: '阿米娅', text: '博士……我明白了。全员撤退！' },
]

const mainScript = [
  { position: 'left', name: '阿米娅', text: '博士，前方区域侦测到源石虫活动迹象。' },
  { position: 'right', name: '凯尔希', text: '根据罗德岛的情报分析，敌方兵力约为我方的两倍。' },
  { position: 'left', name: '阿米娅', text: '虽然形势不利……但只要博士在，我相信一定能找到办法的！' },
  { position: 'right', name: '凯尔希', text: '少说空话。博士，你的决策？' },
  {
    position: 'left', name: '阿米娅', text: '博士，我们该怎么做？',
    choices: [
      { text: '立刻出击，打他们一个措手不及', next: branchReady },
      { text: '等待增援，不要冒险', next: branchWait },
      { text: '撤退，保存实力', next: branchRetreat },
    ]
  },
]

// ===== 启动 =====
const engine = new DialogueEngine()
engine.loadScript(mainScript)
</script>

</body>
</html>
```

---

## 给 AI 的 Prompt（让 AI 帮你生成更多剧情）

```
你是一个视觉小说剧本编辑器。根据我给你的剧情描述，生成 JavaScript 对话数据。

格式严格遵循：
const script = [
  {
    position: "left" | "right",  // 谁在说话
    name: "角色名",
    text: "对话内容",
    // 可选：分支选项
    choices: [
      { text: "选项文字", next: 另一个script数组 }
    ]
  }
]

规则：
1. 两个角色轮流对话，不要连续同一边说超过 2 句
2. 左边通常是主角/友方，右边是对手/NPC
3. 对话结束前至少有一个选项分支
4. 每个分支 3-5 句话
5. 注意节奏：短句紧凑感强，长句用于关键信息

现在请生成：
[描述你的剧情场景]
```

---

## 伪 L2D 进阶：图片分层

真正要做出明日方舟级别的效果，关键在 **分层**：

### 制作流程
```
1. 一张完整立绘 → Photoshop/AI 分成 3-4 层
   - 身体层（body）：躯干、四肢
   - 头发层（hair）：前发、后发
   - 表情层（face）：眼睛、嘴巴
   - 装饰层（deco）：飘带、披风等

2. 每层单独导出 PNG（透明背景）

3. 叠在 .character 容器里，每层不同动画：
   - body: 上下微动 (呼吸)
   - hair: 左右微摆 (飘动)
   - face: scaleY 闪缩 (眨眼)
   - deco: 旋转+位移 (飘带)
```

### CSS 分层动画参数参考
```css
/* 身体 — 呼吸感 */
.char-body {
  animation: bodyBreath 4s ease-in-out infinite;
}
@keyframes bodyBreath {
  0%, 100% { transform: scaleY(1) translateY(0); }
  50% { transform: scaleY(1.003) translateY(-2px); }
}

/* 头发 — 自然飘动 */
.char-hair {
  transform-origin: top center;
  animation: hairSway 5s ease-in-out infinite;
}
@keyframes hairSway {
  0%, 100% { transform: rotate(0deg) translateX(0); }
  30% { transform: rotate(0.5deg) translateX(1.5px); }
  70% { transform: rotate(-0.3deg) translateX(-1px); }
}

/* 眨眼 — 每 4 秒眨一次 */
.char-eyes {
  animation: blink 4s step-end infinite;
}
@keyframes blink {
  0%, 47% { transform: scaleY(1); }
  48% { transform: scaleY(0.05); }
  52% { transform: scaleY(1); }
  100% { transform: scaleY(1); }
}

/* 披风/飘带 — 大幅摆动 */
.char-cape {
  transform-origin: top center;
  animation: capeSway 6s ease-in-out infinite;
}
@keyframes capeSway {
  0%, 100% { transform: rotate(0deg) skewX(0deg); }
  25% { transform: rotate(1deg) skewX(0.5deg); }
  75% { transform: rotate(-1.5deg) skewX(-0.5deg); }
}
```

### 用 AI 分层（不用手动 PS）

```
我有一张游戏角色立绘。请帮我用 Canvas API 实现以下效果：
1. 将立绘加载到 canvas
2. 用 AI 边缘检测（或手动定义区域坐标）将图片分为：
   - 头发区域 (y: 0-30%)
   - 面部区域 (y: 15-35%, x: 30-70%)
   - 身体区域 (y: 30-100%)
3. 对每个区域应用不同的 CSS transform 动画
4. 叠加在一起，看起来像一个会动的立绘

不用完美分割，粗略区域 + 微小动画幅度就够了，因为动画幅度很小（2-3px），
观众根本看不出分割线。
```

---

## 与现有系统整合

### 接入 AI 立绘生成（Banana Pro）
```javascript
// 角色首次登场时，调 AI 生成立绘
async function loadCharacterArt(characterId, description) {
  const response = await fetch('YOUR_BANANA_PRO_API', {
    method: 'POST',
    body: JSON.stringify({ prompt: description, style: 'anime portrait, upper body, transparent background' })
  })
  const blob = await response.blob()
  const url = URL.createObjectURL(blob)

  // 设置为角色立绘
  document.querySelector(`#${characterId} .char-body`).style.backgroundImage = `url(${url})`
}
```

### 接入 inkjs 剧情引擎
```javascript
// 用 ink 脚本驱动对话，引擎负责渲染
const story = new Story(inkJson)

function advanceStory() {
  while (story.canContinue) {
    const text = story.Continue()
    const tags = story.currentTags // ink 标签控制谁说话

    // tags 格式: # speaker:left # name:阿米娅 # emotion:smile
    const speaker = tags.find(t => t.startsWith('speaker:'))?.split(':')[1]
    const name = tags.find(t => t.startsWith('name:'))?.split(':')[1]

    engine.showDialogue({ position: speaker, name, text })
  }

  if (story.currentChoices.length > 0) {
    engine.showChoices(story.currentChoices.map((c, i) => ({
      text: c.text,
      action: () => { story.ChooseChoiceIndex(i); advanceStory() }
    })))
  }
}
```
