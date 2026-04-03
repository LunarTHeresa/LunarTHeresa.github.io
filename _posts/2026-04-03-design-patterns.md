---
layout: post
title: 🎯 设计模式应用
date: 2026-04-03
author: 课程项目组
tags: [设计模式, OOP]
---

## 为什么设计模式重要？

期末考核的核心是 **OOP（面向对象编程）**。不是你会写类，而是你会用设计模式解决实际问题。

我们项目用了 **6种设计模式**，每种都有明确的用途：

---

## 1. 状态模式 — 战斗流程控制

**问题**：回合制游戏有很多状态（抽牌→出牌→战斗→结束），用if-else写会很乱。

**解决方案**：每个状态封装为一个类，实现统一接口：

```java
// 状态接口
public interface BattleState {
    void enter();
    void handleInput(PlayerAction action);
    void update();
    void exit();
}

// 抽牌阶段
public class DrawState implements BattleState {
    private GameEngine engine;
    public DrawState(GameEngine engine) { this.engine = engine; }

    @Override
    public void enter() {
        engine.drawCard();        // 自动抽牌
        engine.setMana(engine.getMaxMana() + 1);  // 能量+1
    }

    @Override
    public void handleInput(PlayerAction action) {
        if (action == PlayerAction.END_DRAW) {
            engine.changeState(new PlayState(engine));  // 切换到出牌阶段
        }
    }
    // ...
}
```

<div class="diagram-box">
    <div class="diagram-title">图1 · 战斗状态机</div>
<pre>
┌─────────┐
│  IDLE   │ ← 游戏初始化
└────┬────┘
     ▼ start()
┌─────────┐  end()  ┌─────────┐
│  DRAW   │ ──────► │  PLAY   │
│  抽牌   │         │  出牌   │
└────┬────┘         └────┬────┘
     │                    │
     │ end()             │ end()
     ▼                    ▼
┌─────────┐  allAttacked ┌─────────┐
│ BATTLE  │ ───────────► │  END    │
│ 战斗    │              │  结算   │
└─────────┘              └─────────┘
</pre>
</div>

---

## 2. 策略模式 — 20+种卡牌效果

**问题**：每张卡牌有不同的效果（伤害、治疗、召唤、buff...），如何优雅地实现？

**解决方案**：每种效果是一个策略类：

```java
// 效果接口（策略）
public interface CardEffect {
    void execute(GameEngine engine, Player owner, Player opponent, int slot);

    String getDescription();
}

// 造成伤害效果
public class DealDamageEffect implements CardEffect {
    private int damage;
    private Target target;  // TARGET_ENEMY_HERO / TARGET_ENEMY_MINION / ...

    public DealDamageEffect(int damage, Target target) {
        this.damage = damage;
        this.target = target;
    }

    @Override
    public void execute(GameEngine engine, Player owner, Player opponent, int slot) {
        if (target == Target.TARGET_ENEMY_HERO) {
            opponent.takeDamage(damage);
        } else if (target == Target.TARGET_MINION_IN_SLOT) {
            engine.getMinionAt(slot).takeDamage(damage);
        }
    }
}

// 治疗效果
public class HealEffect implements CardEffect {
    private int amount;
    // ...
}

// 召唤随从效果
public class SummonEffect implements CardEffect {
    private String cardId;
    // ...
}
```

**卡牌持有效果列表**：
```java
public class Card {
    private String name;
    private int cost;
    private List<CardEffect> battlecryEffects;  // 战吼效果
    private List<CardEffect> deathrattleEffects; // 亡语效果
}
```

---

## 3. 观察者模式 — 战斗事件通知UI

**问题**：后端战斗状态变化了，前端如何知道？

```java
// shared/event/GameEvent.java
public interface GameObserver {
    void onCardPlayed(Card card, int slot);
    void onAttack(Minion attacker, Minion target);
    void onHealthChanged(int playerId, int newHealth);
    void onTurnStart(int currentPlayerId);
    void onGameOver(int winnerId);
    void onBuffApplied(Minion minion, Buff buff);
}

// 后端战斗引擎广播事件
public class GameEngine {
    private List<GameObserver> observers = new ArrayList<>();

    public void addObserver(GameObserver obs) { observers.add(obs); }

    protected void notifyAttack(Minion a, Minion t) {
        for (GameObserver obs : observers) {
            obs.onAttack(a, t);  // 前端收到通知，播放攻击动画
        }
    }
}

// 前端实现观察者接口
public class BattleController implements GameObserver {
    @Override
    public void onAttack(Minion attacker, Minion target) {
        Platform.runLater(() -> {
            // 播放攻击动画
            animator.playAttackAnimation(attacker, target);
        });
    }
}
```

---

## 4. 命令模式 — 支持撤销/重做

**问题**：游戏里"悔棋"功能怎么实现？

```java
// 命令接口
public interface Command {
    void execute();
    void undo();        // 撤销
    String getDescription();
}

// 出牌命令
public class PlayCardCommand implements Command {
    private GameEngine engine;
    private Card card;
    private int slot;
    private Player backupOwner;
    private Minion summonedMinion;

    @Override
    public void execute() {
        backupOwner = engine.getCurrentPlayer();
        engine.playCard(card, slot);
        summonedMinion = engine.getMinionAt(slot);
    }

    @Override
    public void undo() {
        // 1. 从战场移除召唤的随从
        engine.removeMinion(summonedMinion);
        // 2. 抽出的牌放回手牌
        engine.addCardToHand(backupOwner, card);
        // 3. 恢复能量
        engine.refundMana(card.getCost());
    }
}

// 命令管理器
public class CommandManager {
    private Stack<Command> history = new Stack<>();
    private Stack<Command> redoStack = new Stack<>();

    public void execute(Command cmd) {
        cmd.execute();
        history.push(cmd);
        redoStack.clear();  // 新操作清除重做栈
    }

    public void undo() {
        if (!history.isEmpty()) {
            Command cmd = history.pop();
            cmd.undo();
            redoStack.push(cmd);
        }
    }
}
```

---

## 5. 工厂模式 — 卡牌创建

**问题**：20+张卡牌，每张 `new Card(...)` 太乱。

```java
// 卡牌工厂
public class CardFactory {
    private static Map<String, Card> registry = new HashMap<>();

    static {
        // 注册所有卡牌
        registry.put("火焰剑士", createWarriorCard());
        registry.put("冰霜术士", createMageCard());
        // ...
    }

    public static Card create(String cardName) {
        Card template = registry.get(cardName);
        if (template == null) throw new IllegalArgumentException("Unknown card: " + cardName);
        return template.clone();  // 返回克隆体，避免共用
    }

    private static Card createWarriorCard() {
        Card card = new Card();
        card.setName("火焰剑士");
        card.setCost(2);
        card.setAttack(3);
        card.setHealth(2);
        card.setFaction(Faction.WARRIOR);
        card.addEffect(new DealDamageEffect(1, Target.TARGET_ENEMY_HERO));
        return card;
    }
}
```

---

## 6. 组合模式 — Buff叠加效果

**问题**：一个随从可能同时有多个Buff（护盾+灼烧+嘲讽），如何统一管理？

```java
// Buff组合接口
public interface BuffComponent {
    void apply(Minion minion);
    void remove(Minion minion);
    void onTurnStart(Minion minion);
    void onTurnEnd(Minion minion);
    int getPriority();  // 优先级（决定结算顺序）
}

// 单个Buff
public class SingleBuff implements BuffComponent {
    private BuffType type;
    private int value;
    private int duration;  // 持续回合数

    @Override
    public void onTurnEnd(Minion minion) {
        if (type == BuffType.BURNING) {
            minion.takeDamage(value);  // 灼烧每回合扣血
        }
        duration--;
        if (duration <= 0) remove(minion);
    }
}

// Buff组合容器（组合模式）
public class BuffComposite implements BuffComponent {
    private List<BuffComponent> buffs = new ArrayList<>();

    public void add(BuffComponent buff) { buffs.add(buff); }
    public void remove(BuffComponent buff) { buffs.remove(buff); }

    @Override
    public void onTurnEnd(Minion minion) {
        for (BuffComponent buff : buffs) {
            buff.onTurnEnd(minion);  // 递归调用所有子Buff
        }
    }
    // ...
}
```

---

## 设计模式汇总

| 设计模式 | 应用场景 | 核心价值 |
|---------|---------|---------|
| 状态模式 | 战斗流程切换 | 消除大量if-else，状态逻辑清晰 |
| 策略模式 | 20+种卡牌效果 | 新增效果不修改现有代码（开闭原则） |
| 观察者模式 | 后端→前端事件通知 | 解耦战斗逻辑和UI渲染 |
| 命令模式 | 撤销/重做 | 操作可追溯、可回滚 |
| 工厂模式 | 卡牌创建 | 统一管理，配置化 |
| 组合模式 | Buff叠加 | 统一接口处理个体和组合 |

**这6种模式覆盖了OOP的核心原则：开闭原则、依赖倒置、里氏替换。**

<div class="post-footer">
    作者：课程项目组 · <a href="https://github.com/LunarTHeresa">@LunarTHeresa</a>
</div>
