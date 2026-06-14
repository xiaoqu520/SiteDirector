---
title: 常见问题
description: FAQ
---
# ❓ 常见问题 FAQ

> 按场景分类 — 快速定位你的问题

---

## 安装与启动

<details>
<summary><strong>无法启动 / 提示缺少 .NET</strong></summary>

发版包已内置 .NET 8。若仍失败：重新下载最新 `setup.exe` 完整安装；检查杀毒软件是否隔离了 `启动游戏.exe`。
</details>

<details>
<summary><strong>无法初始化音频</strong></summary>

Windows 设置 → 系统 → 声音 → 确保有 **默认播放设备** 且未静音。蓝牙耳机断开时尤其常见。
</details>

<details>
<summary><strong>更新后启动失败</strong></summary>

1. 确认 `Saves/` 和 `settings.json` 仍在
2. 重新运行 `setup.exe` 覆盖安装
3. 勿手动解压 ZIP 到游戏目录
</details>

---

## 存档

<details>
<summary><strong>存档在哪里？</strong></summary>

游戏 exe 同目录 `Saves/`。卸载时安装程序 **默认保留**。
</details>

<details>
<summary><strong>自动存档频率？</strong></summary>

每 **2 个游戏运行日**。重大危机前务必 `Ctrl+S` 手动存档。
</details>

<details>
<summary><strong>读档后人员穿墙？</strong></summary>

v1.4.8+ 读档后强制沿走廊寻路，不再直线穿越。等待数秒即可。
</details>

---

## 玩法机制

<details>
<summary><strong>怎么赢？</strong></summary>

**同时满足**：
1. ≥3 SCP 已收容（999 算 1 个）
2. 全部非 SCP 科技解锁
3. 连续 30 游戏日无 breach

详见 [胜利与失败条件](../12-progression/win-lose.md)
</details>

<details>
<summary><strong>怎么输？</strong></summary>

任一触发：
* 余额 < **−¥100,000**
* 同时 **≥3** SCP loose
* 毁灭协议后编内人员存活 < **30%**
</details>

<details>
<summary><strong>拨款为什么变少了？</strong></summary>

可能原因：
* 审计评级下降（<50 → −15%）
* 基金会审查中（−8%）
* 合同失败罚金
* 运营评分下降

详见 [财政与审计](../06-economy/budget-audit.md)
</details>

<details>
<summary><strong>MTF 派遣要多少钱？</strong></summary>

基础 **¥150,000** × 审计费用乘数：
* 审计 ≥80：×0.95
* 审计 50–79：×1.0
* 审计 30–49：×1.15
* 审计 <30：×1.30
</details>

---

## SCP 与收容

<details>
<summary><strong>SCP-173 为什么一直 breach？</strong></summary>

最常见原因：
1. **无观察岗** — 须研究员轮班保持视线
2. **电力中断** — 观察系统失效
3. **全站封锁** — v1.4.8+ 已修复研究员可继续值守

详见 [观察岗](../07-personnel/orders-observation.md)
</details>

<details>
<summary><strong>SCP-096 怎么防？</strong></summary>

* 收容单元 **严禁** 任何人脸图像/录像/生物特征
* 观察用单向玻璃
* 一旦有人看到脸 → 全站封锁 + MTF
</details>

<details>
<summary><strong>SCP-682 核弹杀不死？</strong></summary>

682 标记 `CanSurviveWarhead`。单发无效，须 **O5 齐射** 多弹头联合。
</details>

<details>
<summary><strong>超期 70 天会怎样？</strong></summary>

**GOC 冻结** — 该 SCP **永久无法收容**，严重审计惩罚。从报告日起倒计时。
</details>

---

## 建造与电力

<details>
<summary><strong>太阳能/风力去哪了？</strong></summary>

v1.6.0 移除。读档时旧设施自动拆除并退款。改用柴油 → 水力 → 地热 → 核电。
</details>

<details>
<summary><strong>工程师为什么乱跑？</strong></summary>

v1.5.0+ 已修复：工程师锁定当前工地直至完工。若仍异常，检查路径是否连通。
</details>

<details>
<summary><strong>房间建了但不工作？</strong></summary>

检查三项：① 与已通电走廊 **连通** ② 电力负载未超限 ③ 所需 **科研人员/工程师** 已到位
</details>

---

## 模组

<details>
<summary><strong>启用模组没效果？</strong></summary>

必须 **返回主菜单并重新进局**。主菜单阶段不加载模组。
</details>

<details>
<summary><strong>Android 能装模组吗？</strong></summary>

不能导入外部模组。仅内置三个官方模组。
</details>

---

## 文档

<details>
<summary><strong>版本号以哪个为准？</strong></summary>

`Directory.Build.props` 的 `GameVersion`（当前 **v1.6.1**）。README 与手册同步该版本。
</details>

仍有疑问？查阅 [术语表](../01-introduction/glossary.md) 或对应系统章节。

---

## 本章导航

- 上一篇：[房间](room-catalog.md)
- 下一篇：[更新日志](changelog.md)
