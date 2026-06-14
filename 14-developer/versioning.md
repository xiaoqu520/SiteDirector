---
title: 版本号规则
description: +0.1与+0.0.1
---
# 🔢 版本号规则

本项目版本号 **按变更规模递增**，不按日历日机械 bump。

| 变更类型 | 递增 | 示例 |
|----------|------|------|
| **大型修改**（新系统、大功能、架构级） | **+0.1**（patch 归零） | `0.10.7` → `0.11.0` |
| **小型修改**（微调、单点修复、UI 小功能） | **+0.0.1** | `0.10.7` → `0.10.8` |

## 发版时需同步

1. [`Directory.Build.props`](../../Directory.Build.props) — `GameVersion`
2. [`CHANGELOG.md`](../../CHANGELOG.md) — 玩家向更新记录
3. [`gitbook/.gitbook.yaml`](../.gitbook.yaml) — `variables.version`（本文档站）
4. Android 若独立变更：`CHANGELOG.mobile.md`

## 规则要点

* 一次发版多项小型改动 → **合并一次** `+0.0.1`
* 大型 + 小型同批 → 按大型 `+0.1`，patch 归零
* CHANGELOG 日期 = **实际发版日**
* 不要「每天一个版本」

## 当前版本

**v1.6.1**（`Directory.Build.props` 为唯一真源）

## GitBook 文档

文档版本变量在 `.gitbook.yaml`：

```yaml
variables:
  version: "1.6.1"
```

发版后更新该字段并同步 GitBook Space（若开启 Git 同步会自动拉取）。

---

## 本章导航

- 上一篇：[Android](mobile-build.md)
- 下一篇：[附录](../appendix/shortcuts.md)
