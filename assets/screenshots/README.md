# 截图资源说明

本目录包含 GitBook 文档插图。

| 文件 | 用途 |
|------|------|
| `../cover.png` | 首页封面 |
| `main-menu.png` | 主菜单 |
| `hud-layout.png` | 四侧栏 HUD |
| `map-overview.png` | 站点地图 |
| `tutorial.png` | 新手教程 |
| `tab-*.png` | 各部门面板 |

当前为 **v1.6.1 风格占位图**（SCP 基金会配色 + 中文标签）。替换为实机截图：

1. 运行 `站点主管-收容协议 启动游戏.exe`
2. 进入对应界面后截图（推荐 1280×720 或 1920×1080）
3. 覆盖同名 PNG 文件
4. 提交 Git 并同步 GitBook

占位图使用 PowerShell + System.Drawing 生成，与 [`styles/website.css`](../styles/website.css) 配色一致。
