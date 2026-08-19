# 合约映射·烟霾作战

Lethe 自定义遭遇战数据包，关卡 ID 为 `8804001`。配套 DLL 会在原版“战斗映射”页追加“烟霾作战”入口，并直接复用原版页签、关卡详情、按钮、敌情面板和战斗效果弹窗。界面不使用 IMGUI/LetheGUI，也不生成自绘背景或边框。Lethe 自定义章节节点仅保留为直接开战的备用入口。

## 关卡基础

- 前排人数：6，启用接力战 `isBatonPassOn`。
- 地图：9-45 的 `Cp9_DustPit`。
- 关卡页 BGM：`custom_sounds/BGM/SMOKE_WAR/SmokeWarTitle.wav`；战斗 BGM：`SmokeWarBattle.wav`。
- 中央展示直接加载 9-45 的 `1339_Gregor_EmperorAppearance` 原版战斗预制体，在原版战斗映射中央框内实时渲染 `Default` idle；20 项词条图标位于 `term_icons/`。
- 基础敌人：蜚蠊皇帝 `1339`，90 级。
- 援助与安瓿机制复用 9-45 的四个原版关卡脚本。
- 关卡本身不写显式敌我站位 ID，避免 9-45 的单人/三人站位限制。
- 旧 G 公司部长使用原版异常战包装 ID `8504`；基础 JSON 不再常驻部长，配套 DLL 会在普通关卡开战前根据 `SW_C01` 幂等加入 `8504 ×3`。

## 词条开关

`modular_lua/smoke_war_terms.lua` 是词条开关的持久化文件。共20项，初始全为 `false`：

- 衰弱：`SW_A01`～`SW_A05`
- 险敌：`SW_B01`～`SW_B04`
- 虫群：`SW_C01`～`SW_C07`
- 变故：`SW_D01`～`SW_D04`

选择界面会自动处理 α/β 互斥、`SW_D01`/`SW_D02` 互斥，以及 `SW_C02`～`SW_C07` 对 `SW_C01` 的前置依赖。敌方详情默认只显示蜚蠊皇帝；选择 `SW_C01` 后会即时追加旧 G 公司部长 ×3。

## 数据结构

```text
SMOKE_WAR/
├─ custom_chapters/CONTRACT_REFLECTION_SMOKE_WAR/
├─ custom_encounters/SMOKE_WAR/
├─ custom_sounds/BGM/SMOKE_WAR/
├─ modular_lua/smoke_war_terms.lua
└─ term_icons/
```

维斯帕数据由配套 DLL 在内存中从原版 `400009` 独立克隆，不需要额外的助手静态表或 `runtime/` 侧车文件。

## 安装

将整个 `SMOKE_WAR` 目录复制到：

```text
<游戏目录>/BepInEx/plugins/Lethe/mods/SMOKE_WAR
```

并将配套的 `SmokeWarContractReflection.dll` 放入 `BepInEx/plugins/`。运行时依赖 Lethe，且 DLL 与数据包必须保持同一版本。
