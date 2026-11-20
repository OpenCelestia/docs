<p align="right"><b>English</b> | <a href="download.md">中文</a></p>

# Get the source

How to check out the Celestia tree with **repo + manifest**. The organization is [OpenCelestia](https://github.com/OpenCelestia). The default branch is **`main`**. GitHub uses `default.xml`. The Gitee mirror is [gitee.com/OpenCelestia](https://gitee.com/OpenCelestia): read the docs there and download the source with `gitee.xml`.

## Requirements

| Item | Requirement |
|------|-------------|
| OS | Linux (Ubuntu 20.04 / 22.04). On Windows, use WSL2 |
| Git | 2.x |
| Python | 3.6+ |
| Disk | 30 GB or more |

## Install repo

Use upstream [git-repo](https://gerrit.googlesource.com/git-repo):

```bash
mkdir -p ~/bin
curl -fsSL https://storage.googleapis.com/git-repo-downloads/repo -o ~/bin/repo
chmod a+x ~/bin/repo
export PATH="${HOME}/bin:${PATH}"
```

## Init and sync

GitHub:

```bash
mkdir -p ~/celestia && cd ~/celestia

repo init \
  -u https://github.com/OpenCelestia/manifest.git \
  -b main \
  -m default.xml

repo sync -c -j4
```

The docs can be read on Gitee, and the same commits can be downloaded from there. A full-tree sync must pass `-m gitee.xml`. `repo init -m default.xml` on the Gitee manifest still fetches every project from GitHub.

```bash
mkdir -p ~/celestia && cd ~/celestia

repo init \
  -u https://gitee.com/OpenCelestia/manifest.git \
  -b main \
  -m gitee.xml

repo sync -c -j4
```

Docs home: [Gitee docs/README.md](https://gitee.com/OpenCelestia/docs/blob/main/README.md). One repository: `git clone -b main https://gitee.com/OpenCelestia/<name>.git`. The repository name for `system/core` is `core`.

`repo sync` checks out every project in the manifest. The manifest `linkfile` entries create `build.sh` and `AGENTS.md` at the workspace root.

## Install the chip SDK

These directories are not in git. Obtain the Anyka SDK through your license channel and unpack it:

```text
celestia/system/
├── core/                     ← synced by repo
├── AnyCloud39AV200/V1.04/    ← Nova
├── AnyCloud39AV100_V2/V2.05/ ← Luna production (V2.04 is luna_dev)
└── Sky39AV130/V1.01/         ← Mira (AOS)
```

Vendor `.ko`, `.so`, `.a`, models, fonts, and burn images are not in the tree either. See [open-source risks](../open-source.en.md).

## Useful commands

```bash
repo status
repo sync -c -j4
repo forall -c 'git pull'
```

## Next

- [Build](build.en.md)
- [Deploy](deploy.en.md)
