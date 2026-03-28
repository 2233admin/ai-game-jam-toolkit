# CCG 实战示例 — 游戏开发场景

## 示例 1: 战斗系统架构选型

### 问题
"回合制战斗系统，应该用状态机还是 ECS（Entity Component System）？"

### 三方回答

**Claude:**
> 用状态机。你是小型 RPG，不是 MMO。
> 状态机直觉好懂：Idle → PlayerTurn → EnemyTurn → CheckResult → Idle。
> ECS 对两个实体的回合制来说是大炮打蚊子。

**Codex:**
> 推荐 ECS，方便后续扩展多精灵战斗、状态效果叠加。
> 可以用轻量 ECS 库 bitecs（3KB gzipped）。

**Gemini:**
> 参考 Pokemon Showdown 的实现——它用的是简单的 turn-based loop + 事件系统。
> 不是状态机也不是 ECS，就是 while 循环 + switch case。

### 共识判定: ⚠️ SPLIT

三方都同意需要清晰的回合管理，但实现方式不同：
- 状态机 vs ECS vs 简单循环

### 最终决策
**选状态机。** 理由：
1. 比赛时间有限，状态机 2 小时能写完，ECS 要半天
2. 代码可读性强，评委看得懂
3. Pokemon Showdown 证明简单方案够用

---

## 示例 2: 精灵捕捉动画

### 问题
"精灵球飞出去 → 摇晃 → 成功/失败 的动画怎么实现？"

### 三方回答

**Claude:**
> 用 Phaser Tween 链：
> ball.moveTo(target) → ball.shake(3次) → success ? sparkle : bounce_back
> 每段用 Promise 串起来，方便控制时序。

**Codex:**
> CSS animation + keyframes 就行，不需要 Phaser Tween。
> 纯 CSS 性能更好，而且前端同学更熟。

**Gemini:**
> 找到一个开源实现：[pokemon-capture-animation CodePen]
> 用 canvas 画的，60fps，可以直接参考。

### 共识判定: ✅ AGREE

三方一致认为用补间动画实现，只是载体不同（Phaser Tween / CSS / Canvas）。

### 最终决策
如果用 Phaser → 用 Phaser Tween（跟引擎一致）。
如果纯 HTML → CSS animation 最简单。
参考 Gemini 找到的 CodePen 做视觉效果。

---

## 示例 3: 紧急 Bug — 战斗结束后画面卡死

### 问题
"战斗胜利后 scene 切换回地图，但画面冻住了，console 没报错"

### 三方回答

**Claude:**
> 99% 是 scene 切换时没销毁战斗 scene 的 Tween 或 Timer。
> 检查 `this.scene.stop('BattleScene')` 有没有调。
> Phaser 的 scene.start() 不会自动 stop 当前 scene。

**Codex:**
> 加 `this.scene.stop()` 在切换前。
> 或者用 `this.scene.switch()` 代替 `this.scene.start()`。

**Gemini:**
> 搜到 Phaser 论坛相同问题：scene 切换时如果有未完成的 Tween，
> Phaser 会等 Tween 完成才切换。加 `this.tweens.killAll()` 在切换前。

### 共识判定: ✅ AGREE

所有人都指向同一个根因：scene 生命周期管理。

### 修复
```javascript
// 战斗结束时
this.tweens.killAll()          // 杀掉所有动画
this.time.removeAllEvents()     // 清掉定时器
this.scene.stop('BattleScene')  // 显式停止
this.scene.start('MapScene')    // 再切换
```

---

## 模板：你自己用

遇到问题时，复制这个模板分别发给 2-3 个 AI：

```
我在做一个 [游戏类型] 的 Web 游戏，用 [技术栈]。

问题/需求：
[描述问题]

当前代码（如果有）：
[粘贴相关代码]

请给出：
1. 你推荐的方案
2. 为什么选这个方案
3. 有什么坑需要注意
```

收到回答后，用 CCG 共识规则合并。
