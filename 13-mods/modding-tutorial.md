---
title: 模组开发教程
description: ModSDK全文
---
# 💻 模组开发教程

> 完整 API 速查见 [api-reference.md](api-reference.md)。玩家安装见 [player-guide.md](player-guide.md)。

本教程面向希望为游戏添加自定义内容的开发者，教你从零开始编写 DLL 模组，并在游戏中加载与管理。

## 目录

1. [概述](#概述)
2. [环境准备](#环境准备)
3. [创建模组项目](#创建模组项目)
4. [实现模组入口](#实现模组入口)
5. [注册自定义房间](#注册自定义房间)
6. [注册 SCP 与科技](#注册-scp-与科技)
7. [C.A.S.S.I.E 站内广播](#cassie-站内广播)
8. [添加贴图资源](#添加贴图资源)
9. [编译与打包](#编译与打包)
10. [安装与游戏内管理](#安装与游戏内管理)
11. [常见问题](#常见问题)
12. [完整示例对照](#完整示例对照)
13. [API 参考速查](#api-参考速查)

---

## 概述

模组采用 **DLL + mod.json** 格式：

```
（游戏目录）/
├── SiteDirector.exe
├── SDL2.dll                  # 运行库，与 exe 同目录
├── soft_oal.dll
├── mods_state.json           # 模组启用状态（游戏自动生成）
├── Mods/
│   └── MyMod/
│       ├── mod.json          # 模组清单（必需）
│       ├── MyMod.dll         # 编译后的模组程序集（必需）
│       └── assets/           # 可选资源目录
│           └── room_icon.png
└── ModSDK/                   # 打包后附带的开发引用（可选）
    ├── SiteDirector.ModSDK.dll
    └── SiteDirector.Core.dll
```

**加载时机：** 模组在进入对局时加载（单人游戏或读档），而非主菜单阶段。在「模组」面板切换启用状态后，需**返回主菜单并重新开始/读档**才能生效。

**注册内容：** 房间、走廊、SCP、科技在 `OnLoad` 中注册；注册表在游戏会话内持久，同一次运行中不会重复加载同一模组。

---

## 环境准备

1. 安装 [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
2. 安装代码编辑器（推荐 [Visual Studio 2022](https://visualstudio.microsoft.com/) 或 [VS Code](https://code.visualstudio.com/)）
3. 获取 SDK 引用，任选其一：
   - **源码开发**：引用仓库内 `src/SiteDirector.ModSDK/SiteDirector.ModSDK.csproj`
   - **发布后开发**：从游戏目录 `ModSDK/` 复制 `SiteDirector.ModSDK.dll` 与 `SiteDirector.Core.dll`

> ModSDK 已通过 `global using` 导出常用命名空间（`RoomTags`、`ScpDefinition`、`CassiePriority` 等），引用 ModSDK 后通常无需手动添加大量 `using`。

---

## 创建模组项目

在任意目录执行：

```powershell
mkdir MyFirstMod
cd MyFirstMod
dotnet new classlib -f net8.0
```

编辑 `.csproj`：

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <AssemblyName>MyFirstMod</AssemblyName>
    <RootNamespace>MyFirstMod</RootNamespace>
  </PropertyGroup>
  <ItemGroup>
    <!-- 方式 A：引用源码（推荐，便于调试） -->
    <ProjectReference Include="路径\SiteDirector.ModSDK\SiteDirector.ModSDK.csproj" />

    <!-- 方式 B：引用已编译 DLL -->
    <!--
    <Reference Include="SiteDirector.ModSDK">
      <HintPath>..\ModSDK\SiteDirector.ModSDK.dll</HintPath>
    </Reference>
    <Reference Include="SiteDirector.Core">
      <HintPath>..\ModSDK\SiteDirector.Core.dll</HintPath>
    </Reference>
    -->
  </ItemGroup>
</Project>
```

**注意：** 只引用 `SiteDirector.ModSDK`（及间接的 `SiteDirector.Core`），**不要**引用游戏本体 `SiteDirector.dll`。

---

## 实现模组入口

创建 `ModEntry.cs`：

```csharp
using SiteDirector.ModSDK;

namespace MyFirstMod;

public sealed class ModEntry : ModBase
{
    public override string Id => "yourname.myfirstmod";

    public override void OnLoad(IModContext context)
    {
        // 在此注册房间、SCP、科技等
    }

    public override void OnUnload()
    {
        // 可选：释放非托管资源
    }
}
```

**要点：**

| 项目 | 说明 |
|------|------|
| `Id` | 全局唯一，建议 `作者名.模组名` |
| `OnLoad` | 进入对局时调用，所有注册在此完成 |
| `ModBase` | 可选基类，也可直接实现 `IMod` |
| 无参构造函数 | 入口类必须可被反射实例化 |

---

## 注册自定义房间

```csharp
context.RegisterRoom(new RoomDefinition
{
    Id = "yourname.custom.lab",       // 全局唯一，勿与内置 ID 冲突
    Name = "异常分析室",
    Description = "模组自定义科研设施概述，显示于检视面板。",  // 可选
    Category = "科研",                 // 须为建造面板已有分类（见下表）
    DefaultWidth = 2,
    DefaultHeight = 2,
    Cost = 30000,
    PowerDraw = 35,                    // 正数=耗电，负数=发电
    StaffRequired = 2,
    MaintenanceCost = 800,
    UpgradeTiers =                      // 可选：扩建升级链，每级一项
    [
        new RoomUpgradeTier(
            TargetWidth: 3, TargetHeight: 3,
            Cost: 25000, DurationMinutes: 60 * 24 * 5,
            PowerDrawBonus: 10, MaintenanceBonus: 200)
    ],
    Tags = RoomTags.Research,
    UnlockTechId = "tech.research",    // 可空
    ContainmentLevel = 0,              // 收容室等级
    Capacity = 0,                      // 宿舍/食堂等容量
    MaxPerSite = 1,                    // 可选：全站该 ID 上限；0 = 不限
    ColorHex = "#9F7AEA",              // 地图边框颜色
    TexturePath = "room_icon.png"      // 相对 assets/，可选
});
```

### UpgradeTiers（可选扩建）

在 `RoomDefinition` 上配置 `UpgradeTiers` 数组，每项为 `RoomUpgradeTier`：

| 字段 | 说明 |
|------|------|
| `TargetWidth` / `TargetHeight` | 扩建后尺寸（格）；锚点 `GridX/GridY` 不变，向右下扩展 |
| `Cost` | 升级费用 |
| `DurationMinutes` | 施工工期（游戏分钟，`60×24` = 1 游戏日） |
| `PowerDrawBonus` | 可选，累加至 `PowerDraw`（负数表示增发电） |
| `MaintenanceBonus` | 可选，累加至 `MaintenanceCost` |
| `StaffRequiredBonus` | 可选，累加至 `StaffRequired` |
| `ResearchSpeedBonus` | 可选，累加至结构体 `BonusResearchSpeed`（科研中心升级后提升研究树速度） |

未配置 `UpgradeTiers` 的房间不可升级。施工期间禁止再次升级或拆除；完工时若扩建区域被占用，将在 1 游戏日后自动重试。

### MaxPerSite（可选数量上限）

| 值 | 含义 |
|----|------|
| `0`（默认） | 全站不限数量 |
| `1` 或更大 | 所有楼层合计，该 `DefinitionId` 最多放置 N 个 |

内置示例：C.A.S.S.I.E 中枢、控制室、科研中心为 `MaxPerSite = 1`；科研实验室为 `MaxPerSite = 2`。已达上限时建造列表灰显，地图预览与放置均被拦截。

### 科研并行规则（内置）

- **科研中心**（`MaxPerSite = 1`）：解锁研究树可见性；`ResearchSpeedBonus` 来自扩建 tier
- **科研实验室**（`MaxPerSite = 2`）：每座通电实验室 = 1 个并行研究槽位；至少 1 座才能开始研究

### 建造面板分类（Category）

注册房间的 `Category` 必须与下列字符串**完全一致**，否则不会出现在建造列表：

| Category 值 | 说明 |
|-------------|------|
| `走廊` | 走廊类（通常用 `RegisterCorridor`） |
| `基础设施` | 电力中继等 |
| `收容` | 收容设施 |
| `后勤` | 宿舍、食堂、医务室等 |
| `生产` | 发电站、生产车间等 |
| `科研` | 实验室 |
| `管理` | 控制室、安保站、C.A.S.S.I.E 中枢等 |

### RoomTags 常用标签

| 标签 | 含义 |
|------|------|
| `Corridor` | 走廊 |
| `CompositeCorridor` | 复合通道（可传电） |
| `NonTraversable` | 不可通行（如电缆通道；传电但不参与人员/SCP 寻路） |
| `PowerSource` | 发电设施 |
| `PowerRelay` | 电力中继 |
| `Containment` | 收容设施 |
| `TemporaryContainment` | 临时收容间（任意 SCP、20 游戏分钟时效；LCZ/HCZ 各最多 3 间） |
| `Housing` | 宿舍 |
| `Food` | 餐饮 |
| `Medical` | 医疗 |
| `Storage` | 仓储 |
| `Production` | 生产 |
| `Research` | 科研 |
| `Security` | 安保 |
| `Management` | 管理 |
| `Cassie` | C.A.S.S.I.E 相关设施（可与 `Management` 组合） |
| `RequiresStorageAdjacency` | 必须邻接仓储室 |

标签影响 AI 寻路、电力削减策略（非关键负荷会关闭 `Housing`、`Food`、`Research`、`Production`）及 C.A.S.S.I.E 响应逻辑，请按实际功能选取。

### 注册走廊

```csharp
context.RegisterCorridor(new CorridorDefinition
{
    Id = "yourname.corridor.reinforced",
    Name = "强化走廊",
    Cost = 2000,
    Tags = RoomTags.Corridor | RoomTags.CompositeCorridor,
    ColorHex = "#2D3748"
});
```

### 电力设计提示

开局站点约 **160 发电 / 153 用电**。高耗电收容室或大量设施可能导致 C.A.S.S.I.E 自动启动负荷削减与备用电力。设计模组房间时请合理设置 `PowerDraw`，或在描述中提示玩家增建发电设施。

### 避免冲突的内置 ID 前缀

内置内容已占用 `room.*`、`corridor.*`、`scp-*`、`tech.*` 等 ID。模组请使用 **`yourname.`** 前缀，例如 `yourname.room.lab`，勿覆盖 `room.system.cassie`、`room.containment.light` 等。

---

## 注册 SCP 与科技

### SCP

```csharp
context.RegisterScp(new ScpDefinition
{
    Id = "yourname.scp-001",
    Name = "SCP-███",
    ObjectClass = "Euclid",
    ThreatLevel = 6,
    RequiredContainmentLevel = 3,
    Description = "模组自定义异常实体。",
    ContainmentProcedures = "2–4 句收容规程摘要，显示在检视面板与收容页。",
    ResearchMilestones = [200, 600, 1500]  // 观察室累积阈值，可选，默认即此三档
});
```

**异常档案字段（v0.3.0+）**

| 字段 | 说明 |
|------|------|
| `Description` | 异常概述，检视面板「概述」区 |
| `ContainmentProcedures` | 收容规程摘要，检视面板「收容规程」区 |
| `ResearchMilestones` | 观察室研究点数里程碑；每跨一档向玩家发送研究简报邮件并发放一次性拨款 |
| `BehaviorFlags` | 与 `NarrativeCatalog` 规程摘要联动，收容页选中 SCP 时显示 |

### 科技

```csharp
context.RegisterTechnology(new TechDefinition
{
    Id = "yourname.tech.advanced",
    Name = "高级异常分析",
    Description = "解锁异常分析室。",
    ResearchCost = 18000,
    PrerequisiteId = "tech.research"
});
```

科技 `Id` 可作为房间的 `UnlockTechId`，玩家研究完成后才能在建造面板看到对应房间。

---

## 邮件与 Sender（v0.3.1+）

游戏内邮件支持可选 `Sender` 字段。简报页会以「发件人 · 主题」格式显示，未设置发件人的邮件仅显示主题。

推荐使用 [`NarrativeCatalog`](../../src/SiteDirector.Core/NarrativeCatalog.cs) 中的发件人常量（模组需引用 `SiteDirector.Core`）：

| 常量 | 含义 |
|------|------|
| `NarrativeCatalog.SenderO5` | O5 议会 |
| `NarrativeCatalog.SenderSecurity` | 站点安保部 |
| `NarrativeCatalog.SenderEthics` | 伦理委员会 |
| `NarrativeCatalog.SenderInternalSec` | 内部安保部 |
| `NarrativeCatalog.SenderResearch` | 异常研究部 |
| `NarrativeCatalog.SenderMtf` | MTF 指挥部 |
| `NarrativeCatalog.SenderEngineering` | 设施工程部 |

```csharp
world.Mails.Add(new MailMessage
{
    Id = $"mail.yourmod.{world.GameMinute}",
    Sender = NarrativeCatalog.SenderSecurity,
    Subject = "收容事故报告 — SCP-XXX",
    Body = """
        站点主管：

        （正文内容）

        此致，
        站点安保部
        """,
    GameMinute = world.GameMinute
});
```

在 `IEventDefinition.OnTriggered` 中发送邮件时，务必设置 `Sender`，以保持与核心游戏一致的基金会公文风格。

---

## C.A.S.S.I.E 站内广播

模组可通过 `EnqueueCassie` 向 C.A.S.S.I.E 播报队列插入消息，显示在地图顶部广播条。

```csharp
context.EnqueueCassie(
    code: "MOD-001",
    headline: "模组通报。",
    message: "异常分析协议已同步至站点网络。",
    priority: CassiePriority.Attention,
    holdSeconds: 5f,
    cooldownKey: "yourname:mod-001",   // 可选，防重复播报
    cooldownMinutes: 30                  // 可选，冷却分钟数
);
```

### CassiePriority

| 优先级 | 典型用途 |
|--------|----------|
| `Routine` | 日常提示 |
| `Attention` | 需要留意的信息 |
| `Warning` | 警告 |
| `Emergency` | 紧急（可抢占当前播报） |
| `Critical` | 最高（立即插播） |

`Emergency` 及以上优先级会走 `EnqueueImmediate` 立即进入队列；其余走带冷却的普通入队。

**示例：在 OnLoad 中播一次欢迎通报**

```csharp
public override void OnLoad(IModContext context)
{
    RegisterContent(context);

    context.EnqueueCassie(
        "MOD-WELCOME",
        "系统通知。",
        "自定义模组内容已加载。",
        CassiePriority.Routine,
        holdSeconds: 4f,
        cooldownKey: $"{Id}:welcome",
        cooldownMinutes: 99999);
}
```

> 玩家可在设置页关闭 C.A.S.S.I.E 系统；关闭后队列不再播报，但模组逻辑不受影响。

---

## 添加贴图资源

在模组目录下创建 `assets/`：

```
Mods/MyMod/
├── mod.json
├── MyMod.dll
└── assets/
    └── room_icon.png
```

代码中引用：

```csharp
// RoomDefinition.TexturePath = "room_icon.png"

var path = context.Assets.GetAssetPath("room_icon.png");
if (path is not null)
{
    // path 为绝对路径，可用于自定义加载逻辑
}

if (context.Assets.AssetExists("room_icon.png"))
{
    // 文件存在
}
```

当前版本会在**建造面板**与**地图房间**上渲染 `TexturePath` 贴图。若未提供 Mod 贴图，游戏会按房间 `RoomTags` 自动使用内置 32×32 程序化图标（仍可被 Mod 贴图覆盖）。

---

## 视觉 Mod 规范

### 房间图标

| 项目 | 建议 |
|------|------|
| 尺寸 | **32×32** 像素（地图最大显示 24px，留缩放余量） |
| 格式 | PNG，带透明底 |
| 路径 | `assets/xxx.png`，在 `RoomDefinition.TexturePath` 中填写相对路径 |
| 优先级 | Mod 贴图 > 内置 tag 图标 > 房间名称文字 |

### 状态叠加

地图上的状态 tint（断电、收容失效、封锁）绘制在**房间边框内侧**，不会遮挡 Mod 贴图中心区域。设计图标时把主要识别信息放在中央，边缘可留 2–4px 空白。

### 关闭特效

玩家可在 **设置 → 屏幕特效 / 地图图例** 关闭 vignette、突破闪白等效果；Mod 逻辑不受影响。

### 示例

```
Mods/MyMod/
├── mod.json
├── MyMod.dll
└── assets/
    └── containment_lab.png   # 32×32，透明底
```

```csharp
context.RegisterRoom(new RoomDefinition
{
    Id = "room.containment.lab",
    Name = "实验收容室",
    TexturePath = "containment_lab.png",
    // ...
});
```

---

## 编译与打包

### 1. 编译

```powershell
dotnet build -c Release
```

### 2. 创建 mod.json

```json
{
  "id": "yourname.myfirstmod",
  "name": "我的第一个模组",
  "version": "1.0.0",
  "author": "你的名字",
  "entryAssembly": "MyFirstMod.dll",
  "entryType": "MyFirstMod.ModEntry",
  "dependencies": []
}
```

| 字段 | 说明 |
|------|------|
| `id` | 与 `IMod.Id` 一致 |
| `name` | 模组面板显示名称 |
| `entryAssembly` | DLL 文件名 |
| `entryType` | 完整类名（`命名空间.类名`） |
| `dependencies` | 依赖的其他模组 `id` 列表 |

### 3. 组装并部署

```
Mods/MyFirstMod/
├── mod.json
├── MyFirstMod.dll
└── assets/          （可选）
```

将整个 `MyFirstMod` 文件夹复制到 **`SiteDirector.exe` 同级** 的 `Mods/` 目录下。

### 4. 发布版游戏目录结构

使用仓库内 `publish.ps1` 打包后，典型布局为：

```
dist/SiteDirector/
├── SiteDirector.exe
├── SDL2.dll
├── soft_oal.dll
├── Mods/ExampleMod/
├── ModSDK/
└── ...
```

模组开发与测试时，以该目录为「游戏根目录」。

---

## 安装与游戏内管理

1. 将模组文件夹放入游戏目录 `Mods/`
2. 启动 `SiteDirector.exe`，主菜单选择「单人游戏」或「读档」进入对局
3. 侧边栏打开 **「模组」** 标签，点击「开/关」切换启用状态
4. **返回主菜单并重新进入对局**，使模组加载/卸载生效

其他操作：

- **打开 Mods 文件夹**：设置页或模组页的快捷按钮
- **mods_state.json**：记录各模组启用状态与加载顺序，位于 exe 同目录
- **存档**：模组注册的内容随对局存档保存结构数据；切换模组后读旧档需确保相关模组仍启用

---

## 常见问题

### 模组未出现在列表中

- 文件夹位于 `Mods/你的模组名/`，而非 `Mods/` 根目录
- 存在格式正确的 `mod.json`
- `entryAssembly` 指定的 DLL 与该文件同目录

### 模组加载失败

- `entryType` 为完整类名（区分大小写）
- DLL 目标框架为 `net8.0`
- 入口类实现 `IMod` 且有无参构造函数
- 未引用 `SiteDirector.dll`（仅 ModSDK / Core）
- 游戏事件日志或简报页可能显示「模组加载失败」及异常摘要

### 房间注册了但建造面板看不到

- `Category` 字符串与内置分类完全一致（见上文表格）
- 房间设置了 `UnlockTechId` 但玩家尚未研究对应科技
- 模组未启用，或未重新进入对局

### ID 冲突

注册时若 ID 已存在会抛异常，导致整个模组加载失败。请使用 `yourname.` 前缀。

### 依赖模组

模组 B 依赖模组 A 时：

```json
"dependencies": ["author.mod-a"]
```

被依赖模组必须先启用；加载顺序按 `mods_state.json` 中的 `loadOrder` 与依赖关系排序。

### RegisterEvent 无效？

`RegisterEvent(IEventDefinition)` 已接入 `EventRegistry`（`src/SiteDirector.Core/Events/EventRegistry.cs`）。实现 `TryTrigger` 返回 `true` 时，游戏会写入事件日志并调用 `OnTriggered`。参考 `mods/ChaosInsurgencyMod/` 与 `mods/Scp173ProcedureMod/`。

---

## 注册随机事件

```csharp
public sealed class MyEvent : IEventDefinition
{
    public string Id => "yourname.event.id";
    public string Title => "事件标题";
    public GameEventSeverity Severity => GameEventSeverity.Warning;

    public bool TryTrigger(GameWorld world, Random rng)
    {
        if (world.GameDay < 5) return false;
        return rng.NextDouble() < 0.002 * SimulationEngine.MinutesPerTick;
    }

    public string GetMessage(GameWorld world) => "事件描述文本";

    public void OnTriggered(GameWorld world)
    {
        // 可选：发邮件、改威胁等级等
    }
}

// OnLoad 中：
context.RegisterEvent(new MyEvent());
```

---

## 模组生命周期钩子（ModHooksBase）

继承 `ModHooksBase` 可响应游戏事件，无需每帧轮询：

| 钩子 | 触发时机 |
|------|----------|
| `OnTick` | 每个模拟 tick |
| `OnBreach` | SCP 突破收容 |
| `OnBreachResolved` | SCP 被重新收容 |
| `OnMonthlySettlement` | 月度财政结算 |
| `OnAssignScp` | 玩家将 SCP 分配至收容室 |
| `OnMissionOffered` | O5 合同下发 |
| `OnMissionCompleted` | 合同完成或失败 |
| `OnMtfReturn` | MTF 派遣返程 |

```csharp
public sealed class ModEntry : ModHooksBase
{
    public override string Id => "yourname.myhooks";

    public override void OnLoad(IModContext context) { }

    public override void OnBreach(GameWorld world, string scpId, Guid roomId)
    {
        context.EnqueueCassie("MOD-01", "注意。", "检测到收容异常。",
            CassiePriority.Emergency);
    }
}
```

---

## SCP 行为标签（BehaviorFlags）

注册 SCP 时可设置 `BehaviorFlags`，启用内置通用规程：

| 标志 | 效果 |
|------|------|
| `RequiresObservation` | 需科研/安保在附近观察，否则可能 breach |
| `FaceSensitive` | 观察室泄露风险；随机触发全站封锁事件 |
| `MoraleBoost` | 收容稳定时提升人员疲劳恢复 |
| `InfectionRisk` | breach 后可能感染其他人员 |

```csharp
context.RegisterScp(new ScpDefinition
{
    Id = "yourname.scp.custom",
    Name = "自定义异常",
    ObjectClass = "Euclid",
    ThreatLevel = 3,
    RequiredContainmentLevel = 2,
    BehaviorFlags = ScpBehaviorFlags.RequiresObservation
});
```

---

## 自定义 SCP 行为（RegisterScpBehavior）

当 `BehaviorFlags` 不足以表达复杂逻辑时，实现 `IScpBehavior` 或继承 `ScpBehaviorBase`：

```csharp
public sealed class Scp682Behavior : ScpBehaviorBase
{
    public override string ScpId => "scp-682";

    public override void OnTick(GameWorld world, PlacedStructure room, Random rng, int deltaMinutes)
    {
        // 每 tick 自定义逻辑
    }

    public override void OnBreach(GameWorld world, PlacedStructure room)
    {
        // breach 时回调
    }
}

// OnLoad 中：
context.RegisterScpBehavior(new Scp682Behavior());
```

---

## 房间贴图（TexturePath）

1. 在模组目录放置 `assets/room_icon.png`（或自定义路径）
2. 注册房间时设置 `TexturePath = "assets/room_icon.png"`
3. 游戏会在**建造面板**与**地图房间**上渲染贴图；无 Mod 贴图时 fallback 为按 `RoomTags` 生成的内置图标，再 fallback 为房间名称文字

也可仅放置 `assets/room_icon.png` 而不设置 `TexturePath`，作为该模组所有房间的默认图标。

---

## 完整示例对照

官方示例位于 `mods/` 目录：

| 模组 | 说明 |
|------|------|
| `ExampleMod/` | 最小入门：房间、SCP、RegisterEvent、OnBreach |
| `Scp173ProcedureMod/` | BehaviorFlags + 观察轮班事件 |
| `ChaosInsurgencyMod/` | RegisterEvent + OnBreach CASSIE 渗透包 |

关键文件：

| 文件 | 作用 |
|------|------|
| `mods/ExampleMod/ModEntry.cs` | 入口与注册 |
| `mods/ExampleMod/mod.json` | 清单 |
| `mods/ExampleMod/ExampleMod.csproj` | 项目配置（含构建后复制 DLL 到模组目录） |

编译示例：

```powershell
dotnet build mods/ExampleMod/ExampleMod.csproj -c Release
```

将 `mods/ExampleMod/` 下的 `mod.json`、`ExampleMod.dll`（及可选 `assets/`）复制到游戏的 `Mods/ExampleMod/`。

打包游戏时，`publish.ps1` 会自动部署示例模组到发布目录。

---

## API 参考速查

```csharp
public interface IMod
{
    string Id { get; }
    void OnLoad(IModContext context);
    void OnUnload();
}

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

public interface IModAssetProvider
{
    string? GetAssetPath(string relativePath);
    bool AssetExists(string relativePath);
}
```

### 相关类型命名空间

| 类型 | 命名空间 |
|------|----------|
| `RoomDefinition`, `CorridorDefinition`, `RoomTags` | `SiteDirector.Core.Build` |
| `ScpDefinition`, `ScpBehaviorFlags`, `IScpBehavior` | `SiteDirector.Core.Containment` |
| `TechDefinition` | `SiteDirector.Core.Research` |
| `CassiePriority` | `SiteDirector.Core.Cassie` |
| `IEventDefinition`, `GameEventSeverity` | `SiteDirector.Core.Events` |
| `MtfDispatchResult`, `ActiveMission` | `SiteDirector.Core.Simulation` |
| `ModBase`, `ModHooksBase`, `ScpBehaviorBase`, `IModHooks`, `IMod`, `IModContext` | `SiteDirector.ModSDK` / `SiteDirector.Core.Modding` |

---

祝你创作愉快。如有问题，欢迎在社区反馈。

---

## 本章导航

- 上一篇：[官方模组](official-mods.md)
- 下一篇：[API](api-reference.md)
