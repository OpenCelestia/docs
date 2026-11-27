<p align="right"><b>English</b> | <a href="devstudio.md">中文</a></p>

# DevStudio guide

DevStudio is the AI engineering tool layer under the workspace root `devstudio/`. It exposes the same capabilities through a **CLI** and IDE **MCP** servers, constrains agents with **Skills**, and remembers the current product / SDK in root **`AGENTS.md`** / **`devstudio.yaml`**.

This page is the **day-to-day usage** entry (install, open an IDE, when to run commands). See [`devstudio/README.md`](../../devstudio/README.md) for the repository overview. Skill bodies live under `devstudio/celestia/` and `devstudio/sdk/`. Capabilities are **not** split by role — the agent picks command groups and Skills from what you ask.

At runtime the agent follows `AGENTS.md` → `devstudio/**/SKILL.md`. **Do not** keep a separate `devstudio/ Skills（`AGENTS.md`）/` copy.

## 1. Pick capability by question

| You ask / want | DevStudio does |
|:---------------|:---------------|
| Which chip / product / form factor? | `init` / `./build.sh` maintains `AGENTS.md` and `devstudio.yaml` |
| First-time setup / new machine | Platform `setup_*` (see §3) |
| Add / change / remove an app and push to board | `app` + `device` (TCP **7777**) |
| Is a Sensor / Flash supported? Off-table plan? | `knowledge` / `plan` / `rules`; say “not listed” if missing |
| Build failed / classify a log | `build` / `build --log` |
| Connect, logs, status, write usr | `device connect` / `log` / `status` / `capture` / `firmware` |
| Open the PC web UI | `devstudio web` (same 7777 device protocol as CLI) |
| HAL adaptation from public headers | `hal plan` / `apply` |
| Memory / CPU before vs after | `device capture` + `tune` (same board, same command, twice) |
| Missing board id / approval / params | Tool emits `NEED_* ASK_UI=…`; use in-IDE Ask / MCP forms |
| Bad build host | `devstudio doctor` (then `--set-host` after ASK_UI) |
| Switch product / register SDK | `devstudio switch` |

Boundaries come from the Skills hit by `AGENTS.md`. If the layer is unclear, start with the celestia overview Skill.

## 2. Pieces

| Part | Location / entry | Role |
|:-----|:-----------------|:-----|
| CLI | `devstudio …` | Terminal / scripts; same functions as MCP |
| MCP | `devstudio mcp --group …`; defs in `devstudio/ide/` | Cursor / VS Code / Claude / Codex groups; referenced from `AGENTS.md` |
| Skill | `devstudio/celestia/`, `devstudio/sdk/` `SKILL.md` files | Agent reads only the hit Skills; **do not** copy into `.cursor` / `.claude` / `.vscode` |
| Workspace constraints | Root `AGENTS.md`, `devstudio.yaml` | Product, SDK, `release_id`, build commands; maintained by init / build |
| Device channel | On-board `application/services/studio` (7777) + host `devstudio device` | Connect, push apps, logs/status, usr partition |
| Web | `devstudio web`; UI under `devstudio/web/` (Node) | Screen / files / build-push; device on 7777, build via `build.sh` |

## 3. First use (install → open IDE)

### 3.1 Install host tools

From `devstudio/`, run the platform entry (installs runtimes if needed, then detects the tree; use `--install-only` for tools only):

| Platform | Command |
|:---------|:--------|
| Windows | `powershell -NoProfile -ExecutionPolicy Bypass -File .\setup_windows.ps1` |
| Linux | `./setup_linux.sh` |
| macOS | `./setup_macos.sh` (cross builds need Linux: SSH / UTM, etc.) |

Sanity check: `devstudio --version`, `devstudio adapter list`. Keep sources and toolchains inside a Linux/WSL tree (do not build only from `/mnt/c` on Windows).

### 3.2 `sdk_celestia`: two stages (no IDE picker)

| Stage | You do | Automatic |
|:------|:-------|:----------|
| **Build** | `./build.sh --product-name <product>` at the workspace root | Maintains product fields in `AGENTS.md` / `devstudio.yaml`. **No** IDE/MCP picker |
| **Open IDE** | Open the **workspace root** in Cursor / VS Code / Claude Code / Codex | Refreshes the matching MCP under `devstudio/ide/` for the current IDE |

Force AGENTS rewrite without building: `./build.sh -d --product-name <product>`.

### 3.3 Other states: full init

| State | Typical layout | You do |
|:------|:---------------|:-------|
| `sdk_customer_app` | Customer app + SDK | `devstudio init` (or `setup_*`) → confirm state → answer app root / HAL / build questions |
| `sdk_only` | SDK version tree only | `devstudio init` at the SDK root → confirm `sdk_only` |

### 3.4 When to run again

| Case | Command |
|:-----|:--------|
| Wrong build host | `devstudio doctor`; then `doctor --set-host …` after ASK_UI |
| Switch product / list options | `devstudio switch` |
| MCP missing / broken global command | Re-run `setup_*`; refresh IDE MCP / reopen window |
| AGENTS only | `./build.sh -d --product-name <product>` or `devstudio init --sync-product <product>` |

## 4. Command groups

| Group | Typical commands | Capability |
|:------|:-----------------|:-----------|
| Project | `init`, `switch`, `doctor`, `project info` | Detect SDK/product, write yaml + AGENTS, check build env |
| Knowledge | `knowledge …`, `driver …`, `sensor …` | Adapter support lists / doc index; never invent missing parts |
| Plan / rules | `plan …`, `rules generate`, `apply` | Off-table CIS / register sequences; apply after approval |
| App | `app add` / `modify` / `remove` / `check` / `recipe` / `apply` | Tablet/cloud app skeletons and checks |
| HAL | `hal plan` / `apply` | HAL adaptation from public headers |
| Build | `build --run`, `build --log` | Run commands from `devstudio.yaml`, or classify existing logs |
| Device | `device connect` / `push` / `log` / `status` / `capture` / … | 7777 connect, push, forensics, firmware (usr/image partitions, not boot) |
| Web | `web` | Original PC UI (screen / files / build-push) |
| Tune | `tune memory` / `cpu` | Compare two captures on the same board/command |
| Report | `report export` | Export task reports |

MCP groups match: `project`, `knowledge`, `rules`, `source`, `build`, `device`, `tune`.

## 5. Relation to `./build.sh`

| Command | Behavior |
|:--------|:---------|
| `./build.sh --product-name <p>` | Normal build; writes `devstudio.yaml` + `AGENTS.md` on first use; rewrites AGENTS only when the product name changes. **No IDE prompt** |
| `./build.sh -d --product-name <p>` | **Only** force-rewrites AGENTS for that product; no build |

If DevStudio is not installed, `build.sh` skips AGENTS sync; run the platform `setup_*` under `devstudio/` first.

## 6. How to talk to the agent

1. State the goal clearly (list parts, add an app, tune CIS, push, read logs).
2. **Approvals / board id / CIS params / env choices** go through in-IDE **Ask** (Cursor AskQuestion or MCP forms).
3. Do not wait in chat for “please reply with approval / board id”; do not set `DEVSTUDIO_UI=desktop` for OS dialogs.
4. Plan first → you approve in Ask → agent writes.
5. Lookups bind to the current `release_id`; missing data answers “not listed”.

## 7. Device push / Web

```bash
devstudio device connect --board <IP>
devstudio device push <app_id> --board <IP>
devstudio device log --board <IP> --seconds 10
devstudio device status --board <IP>
devstudio web [--port 8081] [--product anypi_nova]
```

`devstudio web` serves `http://localhost:8081`. Needs Node.js ≥ 18. Device protocol shares TCP 7777 with the CLI; builds use the same tree’s `./build.sh`.

Typical tablet apps land under `/mnt/install/apps/<id>`; `setting` / `libstudio.so` into read-only usr use `usr.sqsh4` + `device firmware`. On-board process: `application/services/studio` (TCP **7777**).

## 8. IDE FAQ

| Symptom | Fix |
|:--------|:----|
| Agent cannot call DevStudio tools | Ran build/sync (`AGENTS.md` present)? Opened **workspace root** in the current IDE? Refresh MCP / reopen |
| Keeps asking board/approval in chat | Expect in-IDE Ask / MCP form; say to use AskQuestion |
| Answers “not listed” | Normal when the Adapter has no data; do not invent another chip version |
| Build OK but feature wrong | Keep pushing, `device check`, read logs; human confirms device done |
| Multiple boards | Must pick board id in Ask; agent must not guess |

## 9. Related

| Doc | Content |
|:----|:--------|
| [`devstudio/README.md`](../../devstudio/README.md) | Install, two-stage flow, layout, constraints |
| [roles](../quick-start/roles.en.md) | Follow-up questions when params are missing |
| [build](../quick-start/build.en.md) | Layered build and AGENTS sync |
| [application](application.en.md) | Application layer and on-device studio |
