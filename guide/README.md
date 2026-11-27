<p align="right"><a href="README.en.md">English</a> | <b>中文</b></p>

# Guide 文档索引

分层概念与 API 速查。约定与 DevStudio 用法也放在本目录。

## 分层（主文档）

| 层 | 概念 | API |
|:---|:-----|:----|
| Application | [application.md](application.md) | — |
| Framework | [framework.md](framework.md) | [framework-api.md](framework-api.md) |
| Hardware / HAL | [hardware-hal.md](hardware-hal.md) | [hardware-api.md](hardware-api.md) |
| System Core | [system-core.md](system-core.md) | [system-core-api.md](system-core-api.md) |

## 其他专题

| 文档 | 内容 |
|:-----|:-----|
| [api-index.md](api-index.md) | 公开头与 API 导航 |
| [conventions.md](conventions.md) | 文档与代码约定 |
| [devstudio.md](devstudio.md) | DevStudio：CLI / MCP / Skill；两段式；设备 7777 |

Skill 正文在 `devstudio/celestia/`、`devstudio/sdk/`，由工程根 **`AGENTS.md`** 引用（`./build.sh` / `devstudio init` 生成）。**不要**复制到 `.cursor` / `.claude` / `.vscode`。
