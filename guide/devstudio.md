<p align="right"><a href="devstudio.en.md">English</a> | <b>中文</b></p>

# DevStudio 能力说明

DevStudio 是 Celestia 工程里的 AI 研发工具，目录在仓库根 `devstudio/`。它通过 **CLI** 与 IDE 里的 **MCP** 提供同一套能力，用 **Skill** 约束 Agent 怎么改代码、怎么上板，用工程根的 **`AGENTS.md` / `devstudio.yaml`** 记住当前产品与 SDK。

本文是**日常用法主页**（安装、打开 IDE、何时跑命令）。仓库说明见 [`devstudio/README.md`](../../devstudio/README.md)。Skill 正文在 `devstudio/celestia/`、`devstudio/sdk/`。**不按角色划分能力**——Agent 根据你问的问题选命令族与 Skill。

Agent 运行时以 `AGENTS.md` 指向的 `devstudio/**/SKILL.md` 为准；**不要**再维护 `devstudio/ Skills（`AGENTS.md`）/` 副本。

## 1. 按问题选能力

| 你问 / 你要做 | DevStudio 做法 |
|:--------------|:---------------|
| 当前芯片 / 产品 / 形态是什么？ | `init` / `./build.sh` 维护 `AGENTS.md`、`devstudio.yaml` |
| 第一次接入 / 换机装工具 | 对应平台的 `setup_*`（见 §3） |
| 加 / 改 / 删应用，并推到板 | `app` + `device`（TCP **7777**） |
| 某 Sensor / Flash 支不支持？表外怎么走？ | `knowledge` / `plan` / `rules`；未收录就写未收录 |
| 编译失败、日志怎么归类？ | `build` / `build --log`（源码 / 环境 / 构建系统） |
| 连板、抓 log、看状态、写 usr | `device connect` / `log` / `status` / `capture` / `firmware` |
| 打开 PC 页面（画面/文件/编译推送） | `devstudio web`（原版 UI；设备协议与 CLI 共用 7777） |
| HAL 怎么按公开头适配？ | `hal plan` / `apply` |
| 优化前后内存 / CPU 有没有变好？ | `device capture` + `tune`（须同板同命令两次读数） |
| 缺板号 / 要批准 / 补参数 | 工具输出 `NEED_* ASK_UI=…`，用运行中 Ask / MCP 表单 |
| 环境不对 / 要换编译主机 | `devstudio doctor`（失败时按 ASK_UI 选后可用 `--set-host`） |
| 换产品或切 SDK 登记 | `devstudio switch`（见 Skill / 仓库 README） |

细节与边界由 `AGENTS.md` 命中的 Skill 约束；不确定落在哪一层时先读 celestia 总览 Skill。

## 2. 组成

| 部分 | 位置 / 入口 | 作用 |
|:-----|:------------|:-----|
| CLI | `devstudio …` | 终端与脚本调用；与 MCP 进同一套函数 |
| MCP | `devstudio mcp --group …`；定义在 `devstudio/ide/` | Cursor / VS Code / Claude / Codex 分组加载；由 `AGENTS.md` 引用 |
| Skill | `devstudio/celestia/`、`devstudio/sdk/` 下各 `SKILL.md`（路径见 `AGENTS.md`） | Agent 只读命中场景的 Skill，**不复制**到 `.cursor` / `.claude` / `.vscode` |
| 工程约束 | 根目录 `AGENTS.md`、`devstudio.yaml` | 产品、SDK、`release_id`、构建命令、数据根；由 init / build 维护 |
| 设备通道 | 板上 `application/services/studio`（7777）+ 主机 `devstudio device` | 连接、推应用、抓 log/状态、写 usr 分区等 |
| Web | `devstudio web`；UI/服务在 `devstudio/web/`（Node） | 原版页面；设备走 7777，编译走仓库 `build.sh` |

## 3. 第一次怎么用（安装 → 打开 IDE）

### 3.1 装本机工具

在 `devstudio/` 目录按平台跑一条入口（缺运行时会先装，再识别工程；只要运行时可加 `--install-only`）：

| 平台 | 命令 |
|:-----|:-----|
| Windows | `powershell -NoProfile -ExecutionPolicy Bypass -File .\setup_windows.ps1` |
| Linux | `./setup_linux.sh` |
| macOS | `./setup_macos.sh`（芯片交叉编译须 Linux：SSH / UTM 等） |

装完可自检：`devstudio --version`、`devstudio adapter list`。源码与工具链放在 Linux/WSL 树内（Windows 勿把工程只放在 `/mnt/c` 下编译）。

### 3.2 `sdk_celestia`：两段式（不必选 IDE）

| 段 | 你做什么 | 自动发生什么 |
|:---|:---------|:-------------|
| **第一段（构建）** | 在工程根跑 `./build.sh --product-name <产品>` | 维护 `AGENTS.md` 与 `devstudio.yaml` 里的产品。**不会**弹出「选哪个 IDE / MCP」 |
| **第二段（开 IDE）** | 用 Cursor / VS Code / Claude Code / Codex **打开工程根** | **按当前 IDE 自动**刷新 `devstudio/ide/` 下对应 MCP |

强制只重写 AGENTS、不编译：`./build.sh -d --product-name <产品>`。

### 3.3 其它状态：完整 init

| 状态 | 典型目录 | 你做什么 |
|:-----|:---------|:---------|
| `sdk_customer_app` | 客户应用 + SDK | `devstudio init`（或 `setup_*`）→ 确认状态 → 答应用根 / HAL / 编译相关问题 |
| `sdk_only` | 仅 SDK 版本目录 | 在 SDK 根 `devstudio init` → 确认 `sdk_only` |

### 3.4 何时再跑命令

| 情况 | 命令 |
|:-----|:-----|
| 编译环境 / 主机不对 | `devstudio doctor`；按 ASK_UI 选择后可用 `doctor --set-host …` |
| 明确要切产品 / 查可切项 | `devstudio switch` |
| MCP 未加载、全局命令坏了 | 重跑对应 `setup_*`；刷新 IDE MCP / 重开窗口 |
| 仅同步 AGENTS | `./build.sh -d --product-name <产品>` 或 `devstudio init --sync-product <产品>` |

## 4. 命令族一览

| 族 | 典型命令 | 能力 |
|:---|:---------|:-----|
| 工程接入 | `init`、`switch`、`doctor`、`project info` | 识别 SDK/产品状态，写 yaml 与 AGENTS，检查编译环境 |
| 知识与外设 | `knowledge …`、`driver …`、`sensor …` | 查 Adapter 支持列表与文档索引；不编造未收录型号 |
| 计划与规则 | `plan …`、`rules generate`、`apply` | CIS 表外方案、寄存器序列等按规则生成；批准后再合入 |
| 应用 | `app add` / `modify` / `remove` / `check` / `recipe` / `apply` | 增删改 tablet/cloud 应用骨架与检查 |
| HAL | `hal plan` / `apply` | 按公开头做 HAL 适配计划 |
| 构建 | `build --run`、`build --log` | 跑 `devstudio.yaml` 里的命令，或给已有日志分类 |
| 设备 | `device connect` / `push` / `log` / `status` / `capture` / … | 经 7777 连板、推包、取证、固件（usr/镜像分区，不写引导） |
| Web | `web` | 打开原版 PC 页面（画面 / 文件 / 编译推送） |
| 调优 | `tune memory` / `cpu` | 比较同板同命令前后两次指标 |
| 报告 | `report export` | 导出任务报告 |

MCP 分组与上表对应：`project`、`knowledge`、`rules`、`source`、`build`、`device`、`tune`。

## 5. 和 `./build.sh` 的关系

| 命令 | 行为 |
|:-----|:-----|
| `./build.sh --product-name <p>` | 正常编译；首次写 `devstudio.yaml` + `AGENTS.md`；之后仅产品名变化才重写 AGENTS。**不问 IDE** |
| `./build.sh -d --product-name <p>` | **只**强制重写该产品的 `AGENTS.md`，不编译 |

若本机尚未安装 DevStudio，`build.sh` 会跳过 AGENTS 同步；请先在 `devstudio/` 运行对应平台的 `setup_*`。

## 6. 对话怎么用

1. 用中文说清目标（查列表、加应用、调 CIS、推板、看日志等）。
2. **批准 / 板号 / CIS 参数与序列 / 环境选择** 走 IDE **运行中 Ask**（Cursor AskQuestion，或 MCP 聊天内表单）。
3. 不要在聊天里干等「请回复批准 / 请发板号」；禁止设 `DEVSTUDIO_UI=desktop` 弹系统框。
4. 改工程先出计划 → 你在 Ask 里点「是」→ Agent 再写入。
5. 检索只认当前 `release_id`；没有记录会答「未收录」。

## 7. 设备与应用推送 / Web

```bash
devstudio device connect --board <IP>
devstudio device push <app_id> --board <IP>
devstudio device log --board <IP> --seconds 10
devstudio device status --board <IP>
devstudio web [--port 8081] [--product anypi_nova]
```

`devstudio web` 打开原版页面（`http://localhost:8081`）。需要 Node.js ≥ 18。设备协议与 CLI 共用 TCP 7777；编译走同一仓库的 `./build.sh`。

一般 tablet 应用落到 `/mnt/install/apps/<id>`；`setting` / `libstudio.so` 等进只读用户区时走 `usr.sqsh4` + `device firmware`。板上进程为 `application/services/studio`（TCP **7777**）。

## 8. IDE 侧常见问题

| 现象 | 处理 |
|:-----|:-----|
| Agent 调不到 DevStudio 工具 | 是否已跑过构建 / sync（有 `AGENTS.md`）；是否用**当前 IDE**打开了工程根；刷新 MCP 或重开窗口 |
| 一直在聊天里要板号/批准 | 应出现运行中 Ask / MCP 表单；可明确说用 AskQuestion |
| 回答「未收录」 | 当前 Adapter 无该版本数据，属正常；不要让 Agent 用别的芯片版本凑答案 |
| 编译过了功能仍不对 | 继续推板、`device check`、看日志；你确认后才算实机完成 |
| 多块板 | 必须在 Ask 里选定板号；禁止 Agent 自己猜 |

## 9. 相关入口

| 文档 | 内容 |
|:-----|:-----|
| [`devstudio/README.md`](../../devstudio/README.md) | 安装、两段式、目录与约束 |
| [roles](../quick-start/roles.md) | 场景下缺参时的追问清单 |
| [build](../quick-start/build.md) | 分层编译与 AGENTS 同步 |
| [application](application.md) | 应用层与设备端 studio 模块 |
