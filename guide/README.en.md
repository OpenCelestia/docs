<p align="right"><b>English</b> | <a href="README.md">中文</a></p>

# Guide index

Layer concepts and API quick reference. Conventions and DevStudio usage live here too.

## Layers

| Layer | Concepts | API |
|:------|:---------|:----|
| Application | [application.en.md](application.en.md) | — |
| Framework | [framework.en.md](framework.en.md) | [framework-api.en.md](framework-api.en.md) |
| Hardware / HAL | [hardware-hal.en.md](hardware-hal.en.md) | [hardware-api.en.md](hardware-api.en.md) |
| System Core | [system-core.en.md](system-core.en.md) | [system-core-api.en.md](system-core-api.en.md) |

## Other topics

| Doc | Content |
|:-----|:--------|
| [api-index.en.md](api-index.en.md) | Public headers and API navigation |
| [conventions.en.md](conventions.en.md) | Doc and code conventions |
| [devstudio.en.md](devstudio.en.md) | DevStudio: CLI / MCP / Skill; two-stage flow; device 7777 |

Skill bodies live under `devstudio/celestia/` and `devstudio/sdk/`, referenced from workspace-root **`AGENTS.md`** (written by `./build.sh` / `devstudio init`). **Do not** copy them into `.cursor` / `.claude` / `.vscode`.
