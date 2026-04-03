---
layout: post
title: 📅 8周开发计划
date: 2026-04-03
author: 课程项目组
tags: [项目管理, 开发计划]
---

## 开发周期：8周

从立项到演示，共8周有效开发时间。以下是详细里程碑：

<div class="timeline">
    <div class="timeline-item">
        <div class="timeline-week">第 1-2 周</div>
        <div class="timeline-title">📦 搭架子 & 共享模块</div>
        <div class="timeline-desc">
            <strong>后端1（引擎）</strong>：创建Maven项目结构，定义shared模块所有核心类（Card、Player、Buff、GameState枚举）<br>
            <strong>后端2（卡牌）</strong>：设计CardEffect接口体系，定义20张卡牌数据结构<br>
            <strong>后端3（AI）</strong>：设计AIAgent接口，搭好AI骨架<br>
            <strong>前端1（棋盘）</strong>：用Scene Builder画BattleScene主界面，棋盘5格布局<br>
            <strong>前端2（手牌）</strong>：设计手牌区布局，拖拽交互原型<br>
            <strong>前端3（菜单）</strong>：主菜单场景，跳转到各模式
        </div>
    </div>
    <div class="timeline-item">
        <div class="timeline-week">第 3-4 周</div>
        <div class="timeline-title">⚙️ 核心功能开发</div>
        <div class="timeline-desc">
            <strong>后端1（引擎）</strong>：完成战斗状态机（Draw/Play/Battle/End），回合流程，伤害计算<br>
            <strong>后端2（卡牌）</strong>：实现20个CardEffect策略类，Buff系统（嘲讽/冻结/护盾/灼烧）<br>
            <strong>后端3（AI）</strong>：实现RandomAI（随机出牌），贪心算法选择最优出牌<br>
            <strong>前端1（棋盘）</strong>：棋盘渲染，卡片拖入战场，攻击动画基础<br>
            <strong>前端2（手牌）</strong>：抽牌动画，手牌悬停预览，能量/血量显示<br>
            <strong>前端3（菜单）</strong>：AI难度选择，Pve/PVP模式切换
        </div>
    </div>
    <div class="timeline-item">
        <div class="timeline-week">第 5-6 周</div>
        <div class="timeline-title">🤖 AI & 高级功能</div>
        <div class="timeline-desc">
            <strong>后端3（AI）</strong>：实现Minimax + Alpha-Beta剪枝（困难难度），局面评估函数<br>
            <strong>全员联调</strong>：后端3人 + 前端3人结对调试，确保前后端事件通知通畅<br>
            <strong>前端完善</strong>：攻击动画优化，卡牌翻转特效，战斗阶段指示器<br>
            <strong>前端3（菜单）</strong>：卡组构建器，从20张选30张，可保存/加载
        </div>
    </div>
    <div class="timeline-item">
        <div class="timeline-week">第 7 周</div>
        <div class="timeline-title">🧪 测试 & 修Bug</div>
        <div class="timeline-desc">
            全流程测试：<br>
            · PvE完整对战（vs三种AI）<br>
            · PvP双人对战（同一机器轮换）<br>
            · 20张卡牌效果逐一验证<br>
            · Buff叠加/移除正确性<br>
            · 胜负判定逻辑
        </div>
    </div>
    <div class="timeline-item">
        <div class="timeline-week">第 8 周</div>
        <div class="timeline-title">🎬 收尾 & 演示</div>
        <div class="timeline-desc">
            · 代码美化、关键类写JavaDoc注释<br>
            · 写 README.md（项目介绍、玩法说明、架构图）<br>
            · 制作5分钟演示PPT<br>
            · 全组彩排演示流程<br>
            · 准备答辩可能被问到的问题
        </div>
    </div>
</div>

---

## Git分支策略

<div class="diagram-box">
    <div class="diagram-title">图1 · Git分支管理</div>
<pre>
main (主线，所有人PR到这里)
│
├── shared/        ← shared模块，全员共建
│
├── backend/
│   ├── engine     ← 后端1
│   ├── card       ← 后端2
│   └── ai         ← 后端3
│
└── frontend/
    ├── board      ← 前端1
    ├── hand       ← 前端2
    └── menu       ← 前端3

每周五：各分支 → review → 合并到 main
</pre>
</div>

---

## 每周会议建议

| 时间 | 内容 |
|------|------|
| 周一 | 分配本周任务，确认接口约定 |
| 周三 | 中期check-in，block及时暴露 |
| 周五 | 代码合并review，总结进度 |

---

## 验收标准

<div class="info-block">
<strong>最低要求（必须完成）：</strong>
<br>✅ PvE人机对战完整可玩
<br>✅ PvP双人对战完整可玩（同一机器轮换操作）
<br>✅ 至少20张不同卡牌，效果各不相同
<br>✅ 3种AI难度可选
</div>

<div class="success-block">
<strong>进阶目标（有余力做）：</strong>
<br>✅ 卡组构建器（从20+张选牌组30张牌组）
<br>✅ 战斗动画流畅
<br>✅ 支持AI vs AI（观赏模式）
</div>

---

## 答辩可能被问到的问题（提前准备）

| 问题 | 回答要点 |
|------|---------|
| 为什么用观察者模式？ | 解耦后端逻辑和前端UI，后端不知道前端存在 |
| 策略模式和状态模式的区别？ | 策略是"做一件事的不同方法"，状态是"对象的不同状态" |
| 你的AI用了什么算法？ | Minimax + Alpha-Beta剪枝，评估函数=场面优势计算 |
| 如何保证代码质量？ | 分模块开发，接口约定，PR review |
| 卡牌效果如何扩展？ | 新增CardEffect实现类即可，不改现有代码（开闭原则） |

---

*8周时间，按这个计划执行，完全可以做出一个能玩的完整游戏。加油！*

<div class="post-footer">
    作者：课程项目组 · <a href="https://github.com/LunarTHeresa">@LunarTHeresa</a>
</div>
