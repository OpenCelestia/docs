<p align="right"><b>English</b> | <a href="roles.md">中文</a></p>

# Collaborate by scenario (ask to fill gaps)

> This page only covers: for common scenarios, **ask via IDE Ask / MCP forms for missing info**, then land the change. Do not paste fill-in tables; do not make the user fill a form first.
> DevStudio capabilities: [devstudio](../guide/devstudio.en.md). Skill paths: workspace-root `AGENTS.md`.

## 1. How to use

1. The user states a goal or pastes a log / requirement (no need to pick a scenario first)
2. The agent checks the “ask if missing” list below and fills gaps with **Ask / elicitation** (`NEED_* ASK_UI=…`); do not wait in chat
3. When enough info exists: edit → `build` / `./build.sh` → `device` if on-board (board id via Ask too)
4. State PASS/FAIL criteria for verification; see [CONTRIBUTING](../CONTRIBUTING.en.md) for commits

| Scenario | When to use this section | Section |
|---|---|---|
| New product / board | New directory, change BOARD, pick form factor | [§2](#2-new-product--board) |
| Application | Add/change app, cloud, IOTCamera SO | [§3](#3-application) |
| New SoC | New chip SDK + HAL | [§4](#4-new-soc) |
| Debug | Paste log, no frames, crash | [§5](#5-debug) |

---

## 2. New product / board

> Assemble a product or board on an existing SoC. Constraints: celestia-project Skill, [bsp-and-products](bsp-and-products.en.md).

**Hard rules (skip asking if already known)**

- Need baseline product, new product name, `CONFIG_BOARD`, UI form factor (`IOTCamera` / `AI_GLASSES` / `TABLET` / `WEARABLE`)
- Single-app `IOTCamera` lives under `application/packages/ipcamera/<id>/` SO (`module_init` / `module_start`); **not** under `packages/wearable|tablet`
- Main binary name follows `CONFIG_APP_MAIN_TARGET` in `project/<product>/config.mk`; start scripts must launch the same name

**Ask if missing**

| Missing | Ask |
|---|---|
| Baseline / new name | Copy from which `project/<p>/`? New directory name? |
| BOARD | `CONFIG_BOARD` / overlay dir? Reuse board or new? |
| UI form factor | IOTCamera / WEARABLE / TABLET / other? Local display? |
| App id / features | App id? RTSP / nebula / AI / OSD? |
| Board-level scope | Change DTS / rootfs / start scripts / burntool? |
| Board IP | On-board IP? (else `NEED_BOARD ASK_UI=board_ip`) |

---

## 3. Application

> Touch only `application/` (and stream usage if needed); mirror an existing app; use Framework public APIs.

**Ask if missing**

| Missing | Ask |
|---|---|
| Product / form | Which product? tablet / wearable / ipcamera? |
| Mirror which | Which existing package (e.g. `tablet/setting`, `wearable/dashcam`)? |
| App id / features | New id? Preview / record / RTSP / cloud / AI? |
| Streams | Only change `app_stream_usage`? |
| Push | `device push`? Board id? |

---

## 4. New SoC

> Input: chip SDK. Core: per-module `hal/<NewSoC>/`; public headers unchanged; board work in project.

**Ask if missing**

| Missing | Ask |
|---|---|
| SDK path / version | Is `system/<NewSoC>/<Ver>/` in place? |
| SoC name | `CONFIG_SOC_PLATFORM` / `CONFIG_SOC_SDK_VERSION`? |
| Capability list | camera / audio / display / wifi / …? |
| Reference HAL | Which `hal/<RefSoC>/` to mirror? |
| Product / BOARD | New product + board, or HAL only first? |
| OS | Linux or AOS only? |

---

## 5. Debug

> Paste log → classify Bug A–G → minimal fix → retest on board. See celestia-debug Skill (path in `AGENTS.md`).

**Ask if missing**

| Missing | Ask |
|---|---|
| Product | Which `project/<p>/`? |
| Repro | Steps? Intermittent or always? |
| Evidence | Serial / `device log` text? |
| Board id | IP when device forensics needed? |
| Expectation | PASS criteria? |
