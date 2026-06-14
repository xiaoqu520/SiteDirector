---
title: 安装与更新
description: Windows setup与自动更新
---
# 💿 安装与更新

> 版本 **v1.6.1** · Windows 64 位 / Android APK

---

## Windows 安装

{% tabs %}
{% tab title="🆕 新玩家 — 安装向导（推荐）" %}

### 步骤

1. 下载发行包中的 **`站点主管-收容协议 SiteDirector-v1.6.1-setup.exe`**
2. 双击运行安装向导
3. 默认安装路径：`%LOCALAPPDATA%\Programs\SiteDirector`
4. 从开始菜单或桌面快捷方式启动 **「站点主管-收容协议 启动游戏.exe」**

{% hint style="success" %}
发布包已内置 .NET 8 运行时，**无需**单独安装 .NET。
{% endhint %}

### 安装后目录结构

```
SiteDirector/
├── 站点主管-收容协议 启动游戏.exe    ← 运行这个
├── Updater不要点我.exe              ← 勿手动双击
├── CHANGELOG.md
├── settings.json
├── Saves/                          ← 存档
├── Mods/                           ← 官方模组
├── ModSDK/                         ← 模组开发引用
└── assets/
```

{% endtab %}

{% tab title="🔄 已安装 — 自动更新" %}

已安装用户 **无需** 重复下载安装包：

1. 正常启动游戏
2. 启动器读取 `latest.json` 检测新版本
3. 自动下载 ZIP 更新包并 SHA256 校验
4. 覆盖安装目录（**保留** `Saves/` 与 `settings.json`）

| 文件 | 角色 |
|------|------|
| `启动游戏.exe` | 主入口，负责拉起更新检查 |
| `Updater不要点我.exe` | 仅由启动器调用，执行 ZIP 覆盖 |
| `latest.json` | CDN 上的版本清单 |

{% hint style="warning" %}
更新失败时：从官网重新下载最新 **setup.exe** 完整安装。不要手动解压 ZIP 到游戏目录。
{% endhint %}

{% endtab %}

{% tab title="📱 Android APK" %}
见专门章节：[Android 移动端](mobile.md)
{% endtab %}
{% endtabs %}

---

## 系统要求

### Windows

| 项目 | 最低 | 推荐 |
|------|------|------|
| 系统 | Windows 10 64 位 | Windows 11 |
| 处理器 | 双核 2.0 GHz | 四核 3.0 GHz |
| 内存 | 4 GB | 8 GB |
| 显卡 | DirectX 11 | 独立显卡 |
| 分辨率 | 1280×720 | 1920×1080 |
| 磁盘 | 300 MB | 500 MB |
| 音频 | 默认播放设备可用 | — |

### Android

| 项目 | 要求 |
|------|------|
| 系统 | Android 8.0（API 26）+ |
| 图形 | OpenGL ES 2.0 |
| 方向 | **横屏**（强制） |
| 磁盘 | 200 MB |

---

## 存档与设置

| 路径 | 内容 | 卸载时 |
|------|------|--------|
| `Saves/` | 多槽位 `.json` 存档 | 默认保留 |
| `settings.json` | 音量、UI 缩放、特效开关 | 默认保留 |
| `mods_state.json` | 模组启用状态 | 默认保留 |

{% hint style="info" %}
重大操作前按 **Ctrl+S** 快速存档。游戏每 **2 游戏日** 自动存档。
{% endhint %}

---

## 常见安装问题

| 问题 | 解决方案 |
|------|----------|
| 无法初始化音频 | 系统设置 → 启用默认播放设备 |
| 启动闪退 | 重新运行 setup.exe；检查杀毒软件是否拦截 |
| 更新后无法启动 | 删除安装目录后重装 setup.exe（存档在 Saves/ 可保留） |
| Android 签名不一致 | 卸载旧版后重装（debug 包与正式包签名不同） |

---

下一步：[主菜单与存档](main-menu.md) → 了解新游戏初始状态。

---

## 本章导航

- 上一篇：[章节说明](README.md)
- 下一篇：[主菜单与存档](main-menu.md)
