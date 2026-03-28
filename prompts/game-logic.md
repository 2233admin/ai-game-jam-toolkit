# Game Logic 生成模板

> 描述游戏机制 → AI 生成可用逻辑代码

## 通用 Prompt

```
你是一个游戏系统设计师兼前端工程师。根据我的描述，用 TypeScript 实现游戏逻辑模块。

要求：
1. 纯逻辑，不含渲染代码 — 输入/输出用接口定义
2. 所有数值可配置（伤害公式、经验曲线、捕获率）— 抽成 config 对象
3. 导出为 ES Module，可被任意前端框架或游戏引擎导入
4. 包含单元测试用例（至少 3 个边界情况）
5. 注释只写"为什么"，不写"是什么"
```

---

## 战斗系统

```
实现一个回合制战斗系统：

核心规则：
- 双方各出一只精灵，轮流行动
- 每回合可选：攻击（4 技能选 1）/ 换精灵 / 使用道具 / 逃跑
- 伤害公式：damage = (攻击方attack / 防御方defense) * 技能power * 属性克制 * random(0.85, 1.0)
- 属性克制：火>草>水>火，电>水，地>电（用 Map 配置，方便扩展）
- 速度高的先手，速度相同随机

输入接口：
interface Monster {
  name: string
  hp: number; maxHp: number
  attack: number; defense: number; speed: number
  type: ElementType
  skills: Skill[]
}

interface Skill {
  name: string
  power: number
  type: ElementType
  pp: number; maxPp: number
}

输出：
- BattleEngine class，提供 executeRound(action1, action2) → RoundResult
- RoundResult 包含：谁先手、伤害值、是否击倒、战斗是否结束
```

---

## 捕捉系统

```
实现精灵捕捉机制：

捕获率公式：
catchRate = (maxHp * 3 - currentHp * 2) / (maxHp * 3) * ballModifier * statusModifier

- ballModifier: 普通球=1.0, 超级球=1.5, 大师球=255（必捉）
- statusModifier: 正常=1.0, 中毒/烧伤=1.5, 睡眠/冻结=2.0
- 最终判定：random() < catchRate 则成功

动画阶段（返回给前端用）：
1. 扔球（0.5s）
2. 球摇晃 1-3 次（每次 0.8s）— 摇晃次数 = floor(catchRate * 3) clamped to [1,3]
3. 成功 → 星星特效 / 失败 → 球弹开

输出：CaptureEngine.attempt(monster, ball, status) → CaptureResult
CaptureResult: { success: boolean, shakeCount: number, catchRate: number }
```

---

## 进化 / 融合系统

```
实现精灵进化和融合系统：

进化：
- 条件：等级达到阈值 OR 使用特定道具 OR 好感度满
- 进化后：基础属性 * 1.3，可能改变属性，学会新技能
- 数据驱动：进化链定义在 JSON 配置中

融合：
- 两只精灵合成一只新精灵
- 属性 = (A属性 + B属性) * 0.7 + bonus
- 新属性 = A和B属性的组合（双属性）
- 新技能 = 从A和B各继承2个技能
- 外观描述 = A特征 + B特征（用于 AI 生图 prompt）

输出：
- EvolutionEngine.canEvolve(monster) → boolean
- EvolutionEngine.evolve(monster) → Monster
- FusionEngine.fuse(monsterA, monsterB) → { monster: Monster, artPrompt: string }
  // artPrompt 用于调 AI 生图 API
```

---

## 经验值 / 等级系统

```
实现经验值和等级成长：

经验曲线：expNeeded = floor(level^2.5 * 10)
- Lv1→2: 10 exp
- Lv10→11: 3162 exp
- Lv50→51: 176776 exp

战斗经验：expGain = defeatedMonsterLevel * 10 * (isWild ? 1.0 : 1.5)

升级时：
- HP += growthRate.hp * level * 0.1
- Attack += growthRate.attack * level * 0.1
- 每个精灵有自己的 growthRate 配置
- 特定等级学习新技能（从 learnset 配置中查）

输出：LevelSystem.addExp(monster, exp) → LevelUpResult[]
// 可能连升多级，每级返回属性变化和新技能
```

## Tips

- 让 AI 先出接口定义，确认后再写实现
- 数值平衡别纠结，先能跑，后面用 AI 帮你调
- 所有公式参数放 config，方便 A/B 测试
