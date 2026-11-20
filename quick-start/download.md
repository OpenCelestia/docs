<p align="right"><a href="download.en.md">English</a> | <b>中文</b></p>

# 源码下载

本文说明如何用 **repo + manifest** 取得 Celestia 源码树。组织是 [OpenCelestia](https://github.com/OpenCelestia)，默认分支 **`main`**。GitHub 用 `default.xml`。Gitee 镜像是 [gitee.com/OpenCelestia](https://gitee.com/OpenCelestia)，查看文档和下载源码都用这套镜像，全树同步用 `gitee.xml`。

## 前置条件

| 项目 | 要求 |
|------|------|
| 操作系统 | Linux（推荐 Ubuntu 20.04 / 22.04）；Windows 建议 WSL2 |
| Git | 2.x |
| Python | 3.6+ |
| 磁盘空间 | 建议 ≥ 30 GB |

## 安装 repo

使用上游 [git-repo](https://gerrit.googlesource.com/git-repo)：

```bash
mkdir -p ~/bin
curl -fsSL https://storage.googleapis.com/git-repo-downloads/repo -o ~/bin/repo
chmod a+x ~/bin/repo
export PATH="${HOME}/bin:${PATH}"
```

## 初始化与同步

GitHub：

```bash
mkdir -p ~/celestia && cd ~/celestia

repo init \
  -u https://github.com/OpenCelestia/manifest.git \
  -b main \
  -m default.xml

repo sync -c -j4
```

Gitee 上可以打开文档，也可以下载同一批提交。全树同步必须带 `-m gitee.xml`。对 Gitee 的 manifest 使用 `default.xml` 时，`repo sync` 仍会从 GitHub 拉代码。

```bash
mkdir -p ~/celestia && cd ~/celestia

repo init \
  -u https://gitee.com/OpenCelestia/manifest.git \
  -b main \
  -m gitee.xml

repo sync -c -j4
```

只看文档：[Gitee docs/README.md](https://gitee.com/OpenCelestia/docs/blob/main/README.md)。单仓克隆 `git clone -b main https://gitee.com/OpenCelestia/<name>.git`，`system/core` 的仓库名是 `core`。

`repo sync` 按 manifest 拉齐全部源码目录。`manifest` 里的 `linkfile` 会在工作区根生成 `build.sh` 和 `AGENTS.md`。

## 放置芯片 SDK

以下目录不在 Git 中，需自行向安凯微取得 SDK 后解压：

```text
celestia/system/
├── core/                     ← repo 已同步
├── AnyCloud39AV200/V1.04/    ← Nova
├── AnyCloud39AV100_V2/V2.05/ ← Luna 量产（V2.04 为 luna_dev）
└── Sky39AV130/V1.01/         ← Mira（AOS）
```

厂商 `.ko`、`.so`、`.a`、模型、字库和烧录镜像也不在树里。说明见 [开源风险](../open-source.md)。

## 常用命令

```bash
repo status
repo sync -c -j4
repo forall -c 'git pull'
```

## 下一步

- [构建指南](build.md)
- [部署上板](deploy.md)
