---
title: Android构建
description: publish-mobile.ps1
---
# 📱 Android 构建

> 迁移自仓库 `docs/MOBILE_BUILD.md`。

《站点主管：收容协议》提供 **Android APK** 直接安装分发。

## 工程结构

| 项目 | 说明 |
|------|------|
| `src/SiteDirector.Game` | 共享游戏逻辑 |
| `src/SiteDirector.Android` | Android 入口 |
| `src/SiteDirector.Mobile.props` | 移动端源码链接 |

## 环境要求

1. [Android Studio](https://developer.android.com/studio) + SDK Platform 34  
2. [.NET 8 SDK](https://dotnet.microsoft.com/download)  
3. `dotnet workload install android`  
4. **JDK 17+**  
5. `publish.mobile.secrets.json`：

```json
{
  "AndroidSdkDirectory": "C:\\Users\\你\\AppData\\Local\\Android\\Sdk",
  "JavaSdkDirectory": "C:\\Program Files\\Java\\jdk-17"
}
```

## 打包命令

```powershell
.\publish-mobile.ps1 -Configuration Release -Clean
.\publish-mobile.ps1 -Configuration Release -Upload
```

输出：`dist/Android/站点主管-收容协议-v{版本}.apk`  
清单：`dist/latest-android.json`

## 正式签名

**所有 Release 须用同一份 keystore**，否则玩家需卸载重装。

`publish.mobile.secrets.json` 示例：

```json
{
  "AndroidSdkDirectory": "...",
  "JavaSdkDirectory": "...",
  "AndroidKeyStore": "D:\\keys\\sitedirector.keystore",
  "AndroidKeyAlias": "sitedirector",
  "AndroidKeyPass": "密钥密码",
  "AndroidStorePass": "库密码"
}
```

务必备份 keystore 及密码。

## 应用内更新

Release 启动 → 检查 `latest-android.json` → 下载校验 → 系统安装界面。

Debug 跳过更新；可用 `SITE_DIRECTOR_SKIP_UPDATE=1` 临时关闭。

## 玩家安装与触控

见 [Android 移动端](../02-getting-started/mobile.md)。

## 常见问题

| 问题 | 解决 |
|------|------|
| XA5300 找不到 SDK | 配置 `AndroidSdkDirectory` |
| 字体加载失败 | 确认 `assets/fonts/simhei.ttf` 存在 |
| 签名不一致 | 卸载旧版；始终用正式 keystore 发版 |

上传凭据与 PC 版共用 `publish.secrets.json`。

---

## 本章导航

- 上一篇：[Windows](publish.md)
- 下一篇：[版本号](versioning.md)
