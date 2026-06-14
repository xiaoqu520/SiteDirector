---
title: GitBook 发布指南
description: Git Sync 配置、侧栏验收与排错
---

# GitBook 发布指南

本文档说明如何将 `gitbook/` 目录发布到 **GitBook.com**。

---

## 若导入后侧栏很乱、全是英文（必读）

常见现象：首页标题叫 **gitbook**、侧栏出现英文文件名、子页未缩进。

| 原因 | 修复 |
|------|------|
| 未指定文档根目录 | 仓库根须有 [`.gitbook.yaml`](../../.gitbook.yaml)，`root: ./gitbook/` |
| 侧栏标题来自文件名 | 各页 YAML 加 `title:` 中文名 |
| 子页未缩进 | [`SUMMARY.md`](../SUMMARY.md) 用 **2 空格** 递增（勿用 Tab） |

**你需要做的：**

1. Push 含根目录 `.gitbook.yaml` 与 `gitbook/` 的更新  
2. GitBook → **Integrations → Git Sync**：Content root 留空或 `/`（由根 `.gitbook.yaml` 指向 `gitbook/`）  
3. **Settings → General**：Space 名称改为 **站点主管：收容协议**（不要叫 gitbook）  
4. **Sync now** 等待重建  
5. 将 **十、维护** 分组下的「发布指南」「配图清单」设为 **Hidden**（可选）

---

## 三级侧栏验收

Push 后在 Space 中确认可 **展开 3～4 层**，例如：

| 层级 | 示例 |
|------|------|
| 一级 `##` | 五、指挥界面 |
| 二级 | 界面总览 → 部门面板导览 |
| 三级 | 简报、财政、建造… |
| 四级（系统） | 系统百科 → 站点与建造 → 电力网格 |

若子页未缩进，检查 `SUMMARY.md` 缩进为空格且一致。

---

## 前置条件

* GitHub 仓库已推送 `gitbook/` 与根 `.gitbook.yaml`
* GitBook.com 账号（Custom CSS 建议 Organization 计划）

---

## 创建 Space

1. 登录 [GitBook.com](https://www.gitbook.com/)
2. **New space** → **Import from Git**
3. 连接 GitHub 并选择本仓库
4. **不要** 手动把 Content root 设为 `gitbook` 而省略根 `.gitbook.yaml`；推荐仓库根 + 自动识别
5. 确认 `SUMMARY.md` 与 `README.md` 被识别

## 同步设置

| 选项 | 建议 |
|------|------|
| Git Sync | 开启 |
| Bi-directional sync | 可选 |
| Branch | `main` 或默认分支 |

## 应用自定义主题

1. Space → **Settings** → **Customization**
2. **Custom CSS** 粘贴 [`styles/website.css`](../styles/website.css) 全文
3. 保存预览 SCP 深色主题

## 封面与图标

* 首页 `assets/cover.png`
* Space Settings → General → 上传封面作 Space 图标

## 公开访问

1. **Share** → Public
2. 复制 URL 写入根目录 [`README.md`](../../README.md)
3. （可选）绑定自定义域名

---

## 方式 A：Git 同步（推荐）

1. Push 整个仓库（含根 `.gitbook.yaml` + `gitbook/`）  
2. GitBook → Integrations → GitHub → 选择仓库  
3. 推送后自动构建，首页为 `gitbook/README.md`

### 检查清单

- [ ] 仓库根存在 `.gitbook.yaml`，`root: ./gitbook/`
- [ ] `SUMMARY.md` 含 **一～十** 大章与 hub 导览
- [ ] **五、指挥界面** 可展开至 9 个部门子页
- [ ] **六、系统百科** 可展开 6 个 hub → 18 篇系统文
- [ ] **七、SCP 图鉴** 可展开 Safe/Euclid/Keter
- [ ] 各页有 `title:` frontmatter
- [ ] Custom CSS 已粘贴 `website.css`
- [ ] **十、维护** 已 Hidden（可选）
- [ ] P0 配图已上传或保留 `[待补图]`（见 [配图清单](../配图清单.md)）

---

## 方式 B：导入 Markdown 文件夹

1. GitBook → Import → Upload folder → 上传 `gitbook/`
2. 手动确认层级与 `SUMMARY.md` 一致

---

## 维护流程

发版游戏时：

1. 更新根 `.gitbook.yaml` 与 `gitbook/.gitbook.yaml` 的 `version`
2. 更新 `appendix/changelog.md`
3. 新功能 → 更新对应章节 + [配图清单](../配图清单.md)

```mermaid
flowchart LR
  Edit[编辑 gitbook] --> Push[推送 GitHub]
  Push --> Sync[GitBook Sync]
  Sync --> CSS[确认 CSS]
  CSS --> Public[公开访问]
```

---

## 本地预览（可选）

```powershell
npx honkit serve gitbook
```

Honkit 与 GitBook.com 渲染略有差异，以云端为准。

---

## 与 docs/ 的关系

| 路径 | 用途 |
|------|------|
| `gitbook/` | 完整在线手册（本 Space） |
| `docs/PLAYER_README.md` | 发版精简说明（`publish.ps1` 生成） |
| `docs/*.md` | 顶部跳转 GitBook |

---

## 常见问题

**Q：SUMMARY 不显示子页面？**  
A：检查 2 空格缩进，链接为相对路径 `folder/page.md`。

**Q：中文乱码？**  
A：文件 UTF-8 保存。

**Q：图片不显示？**  
A：使用 GitBook 图床或 `assets/screenshots/` 相对路径；Sync 后需路径在 `gitbook/` 内。

---

## 相关

- [配图清单](../配图清单.md)
- [版本号规则](versioning.md)
