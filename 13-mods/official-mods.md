---
title: 官方模组介绍
description: 三个内置模组
---
# 📦 官方模组介绍

发版包 `Mods/` 目录预置以下官方模组，可在模组面板启用。

---

## 示例模组：超重型收容

| 项目 | 值 |
|------|-----|
| ID | `sitedirector.example` |
| 目录 | `mods/ExampleMod/` |
| 用途 | **入门示例** — 最小完整模组 |

**内容**：

* 自定义房间（超重型收容示例）
* 示例 SCP 与科技节点
* `RegisterEvent` 随机事件
* `OnBreach` 钩子演示

**适合**：第一次接触 ModSDK 的开发者，对照源码学习。

---

## SCP-173 观察规程包

| 项目 | 值 |
|------|-----|
| ID | `sitedirector.scp173procedure` |
| 目录 | `mods/Scp173ProcedureMod/` |

**内容**：

* 扩展 SCP-173 **观察轮班** 事件
* `BehaviorFlags.RequiresObservation` 实践
* 观察违规时的 CASSIE 通报

**适合**：已收容 173 的玩家 — 增强观察岗玩法深度。

---

## 混沌分裂者事件包

| 项目 | 值 |
|------|-----|
| ID | `sitedirector.chaosinsurgency` |
| 目录 | `mods/ChaosInsurgencyMod/` |

**内容**：

* `RegisterEvent` 混沌分裂者渗透事件
* Breach 时 CASSIE 紧急播报
* 外部袭击叙事邮件

**适合**：想要额外危机与叙事挑战的进阶玩家。

---

## 启用建议

| 玩家类型 | 建议 |
|----------|------|
| 新手 | 先 **不启用**，熟悉原版后再开 173 规程包 |
| 寻求挑战 | 173 规程 + 混沌分裂者 |
| 模组作者 | 启用示例模组并对照 `ModEntry.cs` |

## 源码位置

```
mods/
├── ExampleMod/
├── Scp173ProcedureMod/
└── ChaosInsurgencyMod/
```

编译：

```powershell
dotnet build mods/ExampleMod/ExampleMod.csproj -c Release
```

`publish.ps1` 会自动将官方模组部署到发版目录。

---

## 本章导航

- 上一篇：[玩家指南](player-guide.md)
- 下一篇：[开发教程](modding-tutorial.md)
