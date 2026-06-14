---
title: API速查
description: IMod接口
---
# 📖 API 速查

> 完整教程见 [模组开发教程](modding-tutorial.md)。

## IMod 接口

```csharp
public interface IMod
{
    string Id { get; }
    void OnLoad(IModContext context);
    void OnUnload();
}
```

## IModContext

```csharp
public interface IModContext
{
    string ModId { get; }
    string ModDirectory { get; }
    void RegisterRoom(RoomDefinition room);
    void RegisterCorridor(CorridorDefinition corridor);
    void RegisterScp(ScpDefinition scp);
    void RegisterTechnology(TechDefinition tech);
    void RegisterEvent(IEventDefinition evt);
    void RegisterScpBehavior(IScpBehavior behavior);
    void EnqueueCassie(string code, string headline, string message,
        CassiePriority priority, float holdSeconds = 4f,
        string? cooldownKey = null, int cooldownMinutes = 0);
    IModAssetProvider Assets { get; }
}
```

## mod.json 字段

| 字段 | 说明 |
|------|------|
| `id` | 与 `IMod.Id` 一致 |
| `name` | 面板显示名 |
| `version` |  semver |
| `author` | 作者 |
| `entryAssembly` | DLL 文件名 |
| `entryType` | 完整类名 |
| `dependencies` | 依赖模组 id 数组 |

## ModHooksBase 钩子

| 钩子 | 时机 |
|------|------|
| `OnTick` | 每模拟 tick |
| `OnBreach` | SCP breach |
| `OnBreachResolved` | 重收容完成 |
| `OnMonthlySettlement` | 月结 |
| `OnAssignScp` | 分配 SCP 至房间 |
| `OnMissionOffered` | O5 合同下发 |
| `OnMissionCompleted` | 合同结束 |
| `OnMtfReturn` | MTF 返程 |

## ScpBehaviorFlags

| 标志 | 效果 |
|------|------|
| `RequiresObservation` | 需观察岗 |
| `FaceSensitive` | 面部敏感（096 类） |
| `MoraleBoost` | 士气提升 |
| `InfectionRisk` | 感染风险 |
| `MemeticHazard` | 模因危害 |
| `AggressiveEntity` | 主动攻击 |
| `Predatory` | 掠食行为 |

## CassiePriority

`Routine` → `Attention` → `Warning` → `Emergency` → `Critical`

## 命名空间

| 类型 | 命名空间 |
|------|----------|
| `RoomDefinition`, `RoomTags` | `SiteDirector.Core.Build` |
| `ScpDefinition`, `IScpBehavior` | `SiteDirector.Core.Containment` |
| `TechDefinition` | `SiteDirector.Core.Research` |
| `CassiePriority` | `SiteDirector.Core.Cassie` |
| `IEventDefinition` | `SiteDirector.Core.Events` |
| `ModBase`, `ModHooksBase` | `SiteDirector.ModSDK` |

## ID 命名规范

使用 **`yourname.`** 前缀，勿覆盖 `room.*`、`scp-*`、`tech.*` 内置 ID。

---

## 本章导航

- 上一篇：[教程](modding-tutorial.md)
- 下一篇：[发版导览](../14-developer/hubs/开发发布.md)
