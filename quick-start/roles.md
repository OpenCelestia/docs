<p align="right"><a href="roles.en.md">English</a> | <b>中文</b></p>

# 按场景协作（追问补齐）

> 本文只说明：常见场景下，**缺什么信息就用 IDE Ask / MCP 表单追问**，再落地；不要贴表、不要让用户先填表。
> DevStudio 按问题选能力见 [devstudio](../guide/devstudio.md)；Skill 路径见工程根 `AGENTS.md`。

## 1. 怎么用

1. 用户直接说目标或贴 log / 需求（不必先选场景）
2. AI 对照本节「应追问」清单，缺项用 **Ask / elicitation** 补齐（`NEED_* ASK_UI=…`），禁止聊天干等
3. 信息够了再改代码 → `build` / `./build.sh` → 需要上板则 `device`（板号同样 Ask）
4. 验证说清 PASS/FAIL 判据；提交规范见 [CONTRIBUTING](../CONTRIBUTING.md)

| 场景 | 何时对照本节 | 节 |
|---|---|---|
| 新产品 / 新板 | 新目录、换 BOARD、定形态 | [§2](#2-新产品--新板) |
| 应用 | 加/改 App、云、IOTCamera SO | [§3](#3-应用) |
| 新芯片 | 新 SoC SDK + HAL | [§4](#4-新芯片) |
| 调试 | 贴 log、不出帧、崩溃 | [§5](#5-调试) |

---

## 2. 新产品 / 新板

> 已有 SoC 上组装产品或板。约束：celestia-project Skill、[bsp-and-products](bsp-and-products.md)。

**硬约束（已知则不必再问）**

- 须有基线产品、新产品名、`CONFIG_BOARD`、UI 形态（`IOTCamera` / `AI_GLASSES` / `TABLET` / `WEARABLE`）
- `IOTCamera` 单应用放 `application/packages/ipcamera/<id>/` SO（`module_init`/`module_start` 保活），**不要**进 `packages/wearable|tablet`
- 主程序名以 `project/<产品>/config.mk` 的 `CONFIG_APP_MAIN_TARGET` 为准；启动脚本必须拉同一名字

**AI 应追问（缺哪项问哪项）**

| 缺什么 | 怎么问（Ask） |
|---|---|
| 基线 / 新产品名 | 从哪个 `project/<p>/` 复制？新产品目录名？ |
| BOARD | `CONFIG_BOARD` / overlay 目录名？复用旧板还是新建？ |
| UI 形态 | IOTCamera / WEARABLE / TABLET / 其他？有无本地显示？ |
| 单应用与能力 | 应用 id？要不要 RTSP / nebula / AI / OSD？ |
| 板级改动范围 | 是否改 DTS / rootfs / 启动脚本 / burntool？ |
| 验证板号 | 上板 IP？（未给则 `NEED_BOARD ASK_UI=board_ip`） |

---

## 3. 应用

> 只动 `application/`（及必要的 stream 用量）；仿现有 App；走 Framework 公开接口。

**AI 应追问**

| 缺什么 | 怎么问 |
|---|---|
| 产品 / 形态 | 哪个产品？tablet / wearable / ipcamera？ |
| 仿谁 | 对照哪个现有包（如 `tablet/setting`、`wearable/dashcam`）？ |
| 应用 id 与能力 | 新 id？要预览 / 录像 / RTSP / 云 / AI？ |
| 码流 | 是否只改 `app_stream_usage`？ |
| 推板 | 是否 `device push`？板号？ |

---

## 4. 新芯片

> 输入：芯片 SDK。核心：逐模块 `hal/<NewSoC>/`；公开头不改；板级走 project。

**AI 应追问**

| 缺什么 | 怎么问 |
|---|---|
| SDK 路径 / 版本 | `system/<NewSoC>/<Ver>/` 是否已放好？ |
| SoC 名 | `CONFIG_SOC_PLATFORM` / `CONFIG_SOC_SDK_VERSION`？ |
| 能力清单 | camera / audio / display / wifi / …？ |
| 参考基线 | 对照哪个 `hal/<RefSoC>/`？ |
| 产品 / BOARD | 新产品名与板型，还是先只做 HAL？ |
| OS | Linux 还是仅 AOS？ |

---

## 5. 调试

> 贴 log → 归 Bug A–G → 最小修复 → 上板复测。见 celestia-debug Skill（路径在 `AGENTS.md`）。

**AI 应追问**

| 缺什么 | 怎么问 |
|---|---|
| 产品 | 哪个 `project/<p>/`？ |
| 复现 | 步骤？偶发还是必现？ |
| 证据 | 串口 / `device log` 原文？ |
| 板号 | 需要连板取证时的 IP？ |
| 期望 | PASS 判据？ |
