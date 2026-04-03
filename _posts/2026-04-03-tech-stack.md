---
layout: post
title: 🛠️ 技术栈选择
date: 2026-04-03
author: 课程项目组
tags: [技术选型]
---

## 前端技术

| 选项 | 优点 | 缺点 | 最终选择 |
|------|------|------|---------|
| Swing | Java内置，无需额外安装 | 界面较丑，开发效率低 | ❌ |
| **JavaFX** | 现代化UI、支持动画、SceneBuilder拖拽开发 | 需要单独安装SDK | ✅ **选用** |
| libGDX | 游戏开发神器，效果强大 | 学习曲线陡峭 | ❌ |

**JavaFX 21 + Scene Builder** 是最佳选择：
- Scene Builder 可以可视化拖拽UI，不用手写XML布局
- 内置动画支持，适合做卡牌翻转、攻击特效
- 教学重点是OOP，不需要在游戏引擎上消耗太多精力

---

## 后端技术

| 选项 | 优点 | 缺点 | 最终选择 |
|------|------|------|---------|
| 纯Java | 课程要求，直击OOP训练目标 | 无 | ✅ **选用** |
| Spring Boot | 生态强大 | 课程要求纯Java，且本项目不需要Web服务 | ❌ |

后端**不做Web服务**，纯本地Java逻辑，这样OOP训练更纯粹。

---

## 构建工具

**Maven** — 无悬念的选择：

```xml
<!-- 父 pom.xml 管理三个子模块 -->
<modules>
    <module>shared</module>    <!-- 共享模型 -->
    <module>backend</module>   <!-- 后端逻辑 -->
    <module>frontend</module>  <!-- 前端UI -->
</modules>
```

---

## 数据库？不需要！

卡牌游戏有几个特点：
- 回合制离线游戏，不需要实时通信
- 卡牌数据量小（20+张），直接写死在代码里
- 不需要存储用户进度

因此**不引入任何数据库**，数据用 `resources/cards.json` 管理即可：

```json
[
  {
    "id": 1,
    "name": "火焰剑士",
    "cost": 2,
    "attack": 3,
    "health": 2,
    "type": "MINION",
    "faction": "WARRIOR",
    "rarity": "COMMON"
  }
]
```

---

## 版本控制：Gitee（国产）

GitHub在国内访问不稳定，**码云（Gitee）** 是更好的选择：
- 中文界面，国内访问速度快
- 私有仓库对学生免费
- 支持Pull Request代码审查

---

## 最终技术栈汇总

```
┌─────────────┬──────────────────────────────┐
│  构建工具    │  Maven                        │
│  前端UI     │  JavaFX 21 + Scene Builder   │
│  后端逻辑   │  纯Java（无框架）              │
│  数据库     │  无（JSON配置代替）            │
│  版本控制   │  Git + Gitee                  │
│  开发工具   │  IntelliJ IDEA（学院邮箱免费）  │
│  语言版本   │  Java 17+                     │
└─────────────┴──────────────────────────────┘
```

全部工具链均为**免费/开源**，没有任何商业付费组件。

<div class="post-footer">
    作者：课程项目组 · <a href="https://github.com/LunarTHeresa">@LunarTHeresa</a>
</div>
