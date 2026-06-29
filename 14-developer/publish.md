---
title: Windows打包发布
description: publish.ps1
---
# 📦 Windows 打包发布

> 迁移自仓库 `docs/PUBLISH.md`，面向开发者。

版本号仅在仓库根目录 [`Directory.Build.props`](../../Directory.Build.props) 的 `GameVersion` 维护。  
文件名与 [`UpdateConstants`](../../src/SiteDirector.Core/Updates/UpdateConstants.cs) 对齐，由 `tools/PublishMetadata` 输出 JSON。

## 发行模型（双通道）

| 产物 | 受众 | 用途 |
|------|------|------|
| **`…-setup.exe`** | 新玩家 | Inno Setup 安装到 `%LOCALAPPDATA%\Programs\SiteDirector` |
| **`…SiteDirector-vX.Y.Z.zip`** | 已安装用户 | 游戏内自动更新（保留 `Saves/`、`settings.json`） |
| **`latest.json`** | 客户端 | 含 ZIP/setup URL 与 SHA256 |
| **`CHANGELOG.md`** | 游戏内 | 上传 CDN，与打包目录副本一致 |

**不要**向玩家主推「解压 ZIP 即玩」；ZIP 仅服务增量更新。

## 脚本结构

```
publish.ps1
scripts/publish/
  Common.ps1, Build.ps1, Layout.ps1, Release.ps1, Mobile.ps1
tools/PublishMetadata/
installer/SiteDirector.iss
docs/PLAYER_README.md  → 分发「玩家说明.md」
publish-mobile.ps1     → 兼容入口（等价 publish.ps1 -Apk）
```

## 打包流程

`publish.ps1` 顺序：

1. 清理 dist → 2. 图标 → 3. Release 编译 → 4. 发布 exe → 5. 更新器  
6. ModSDK → 7. 版本 → 8. CHANGELOG → 9. 玩家说明 → 10. 清理  
11. 校验 → 12. Inno setup → 13. latest.json → 14. 上传（`-Upload`）

## 前置依赖

* [Inno Setup 6](https://jrsoftware.org/isinfo.php)
* `publish.secrets.json`（复制 example，勿提交）

## 发版命令

```powershell
.\publish.ps1
.\publish.ps1 -Upload
.\publish.ps1 -Apk
.\publish.ps1 -Apk -Upload
```

## 输出

| 路径 | 说明 |
|------|------|
| `dist/SiteDirector/` | staging |
| `dist/…SiteDirector-v{版本}.zip` | 更新包 |
| `dist/…-setup.exe` | 安装包 |
| `dist/latest.json` | 更新清单 |
| `dist/latest-android.json` | Android 清单（`-Apk`） |
| `dist/Android/…v{版本}.apk` | Android 安装包（`-Apk`） |

公共 URL：`https://sitedirector.cn-sy1.rains3.com/latest.json`

## staging 必含项

* `站点主管-收容协议 启动游戏.exe`
* `Updater不要点我.exe`
* `Mods/`、`ModSDK/`、`assets/`
* `VERSION`、`CHANGELOG.md`
* `站点主管：收容协议 — 玩家说明.md`
* `模组开发示例.zip`

## Android

见 [Android 构建](mobile-build.md)。

---

## 本章导航

- 上一篇：[发版导览](../14-developer/hubs/开发发布.md)
- 下一篇：[Android](mobile-build.md)
