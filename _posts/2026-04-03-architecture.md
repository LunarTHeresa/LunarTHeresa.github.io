---
layout: post
title: 🏗️ 系统架构设计
date: 2026-04-03
author: 课程项目组
tags: [架构设计]
---

## 整体架构：前后端分离

```
┌─────────────────────────────────────────────────┐
│              Frontend (JavaFX)                  │
│                                                 │
│   ┌─────────┐  ┌─────────┐  ┌─────────────┐   │
│   │  棋盘    │  │  手牌   │  │  菜单系统    │   │
│   │ BoardUI │  │ HandUI  │  │  MenuUI      │   │
│   └────┬────┘  └────┬────┘  └──────┬──────┘   │
│        └───────────┼──────────────┘           │
│                    │ 观察者模式（事件通知）       │
├────────────────────┼───────────────────────────┤
│                    ▼                           │
│   ┌──────────────────────────────────────┐    │
│   │        Shared 模块（共享模型）         │    │
│   │  Card / Player / Buff / GameState    │    │
│   └──────────────────────────────────────┘    │
├─────────────────────────────────────────────────┤
│              Backend (Core Java)                │
│                                                 │
│   ┌─────────────┐  ┌──────────────┐           │
│   │  GameEngine │  │ CardEffectMgr │           │
│   │  (状态机)   │  │  (效果系统)   │           │
│   └─────────────┘  └──────────────┘           │
│   ┌─────────────┐                              │
│   │ AIAgent     │                              │
│   │ (随机/贪心/Minimax)                        │
│   └─────────────┘                              │
└─────────────────────────────────────────────────┘
```

**核心思想**：前端只管"怎么显示"，后端只管"怎么计算"，两者通过共享模型和事件机制通信。

---

## Maven 多模块结构

```
cardgame/
├── pom.xml              ← 父POM，统一版本管理
│
├── shared/              ← 【全员共建】共享模块
│   ├── src/main/java/com/cardgame/shared/
│   │   ├── model/       ← 核心数据模型
│   │   │   ├── Card.java
│   │   │   ├── Player.java
│   │   │   ├── Buff.java
│   │   │   └── GameState.java
│   │   ├── dto/         ← 前后端传输对象
│   │   ├── effect/      ← 效果接口（策略模式）
│   │   └── event/        ← 事件类型定义
│   └── pom.xml
│
├── backend/             ← 【后端3人】负责
│   ├── src/main/java/com/cardgame/backend/
│   │   ├── game/        ← 战斗引擎 + 状态机
│   │   ├── card/        ← 卡牌效果实现
│   │   └── ai/          ← AI决策
│   └── pom.xml
│
└── frontend/            ← 【前端3人】负责
    ├── src/main/java/com/cardgame/frontend/
    │   ├── board/       ← 棋盘UI + 动画
    │   ├── hand/        ← 手牌UI + 拖拽
    │   ├── menu/        ← 菜单 + 结算
    │   └── controller/  ← JavaFX控制器
    └── pom.xml
```

---

## 团队分工

6人分为两队，**前后端各3人**：

<div class="team-grid">
    <div class="team-card">
        <div class="role-icon">⚙️</div>
        <div class="role-name">后端 · 战斗引擎</div>
        <div class="role-desc">回合状态机、伤害计算、观察者事件广播</div>
    </div>
    <div class="team-card">
        <div class="role-icon">🃏</div>
        <div class="role-name">后端 · 卡牌系统</div>
        <div class="role-desc">20+卡牌效果策略实现、Buff框架、效果组合</div>
    </div>
    <div class="team-card">
        <div class="role-icon">🤖</div>
        <div class="role-name">后端 · AI模块</div>
        <div class="role-desc">RandomAI / GreedyAI / MinimaxAI 三种难度</div>
    </div>
    <div class="team-card">
        <div class="role-icon">🎨</div>
        <div class="role-name">前端 · 棋盘渲染</div>
        <div class="role-desc">JavaFX棋盘格子、随从摆放、攻击动画</div>
    </div>
    <div class="team-card">
        <div class="role-icon">✋</div>
        <div class="role-name">前端 · 手牌系统</div>
        <div class="role-desc">手牌显示、拖拽出牌、抽牌动画、悬停预览</div>
    </div>
    <div class="team-card">
        <div class="role-icon">📋</div>
        <div class="role-name">前端 · 菜单界面</div>
        <div class="role-desc">主菜单、AI难度选择、卡组构建器、结算界面</div>
    </div>
</div>

---

## 通信机制：观察者模式

后端战斗状态变化，通过观察者接口通知前端更新UI：

```java
// shared/event/GameEvent.java
public interface GameObserver {
    void onCardPlayed(Card card, int slot);
    void onAttack(Minion attacker, Minion target);
    void onHealthChanged(int playerId, int newHealth);
    void onTurnStart(int currentPlayerId);
    void onGameOver(int winnerId);
}
```

```java
// backend/game/GameEngine.java
public class GameEngine {
    private List<GameObserver> observers = new ArrayList<>();

    public void addObserver(GameObserver obs) {
        observers.add(obs);
    }

    // 战斗事件触发时，通知所有观察者
    protected void notifyAttack(Minion attacker, Minion target) {
        for (GameObserver obs : observers) {
            obs.onAttack(attacker, target);
        }
    }
}
```

前端实现这个接口，战斗逻辑一变，UI自动刷新——**零耦合**。

<div class="post-footer">
    作者：课程项目组 · <a href="https://github.com/LunarTHeresa">@LunarTHeresa</a>
</div>
