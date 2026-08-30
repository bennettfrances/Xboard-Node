# 中文 README 规范化 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将 `README.md` 重构为中文优先的标准项目说明，并加入可复现、可验证的维护者发布流程。

**Architecture:** 只修改根目录 `README.md`，以 `install.sh`、`cmd/xbctl/main.go`、`.github/workflows/ci.yml`、`config.yml.example`、`Makefile` 和扩展文档为事实来源。README 按安装用户、运维人员、仓库维护者的阅读顺序组织；最终通过静态检查、源码对照、GitHub Release 核对和 dev 分支 CI 验证。

**Tech Stack:** Markdown、Bash 示例、PowerShell 验证命令、Git、GitHub CLI、GitHub Actions

---

## 文件结构

- Modify: `README.md` — 项目唯一入口文档，负责项目介绍、安装、运维、发布和故障排查。
- Reference only: `install.sh` — 安装器动作、模式、参数、系统要求和默认 `dev` 通道。
- Reference only: `cmd/xbctl/main.go` — `xbctl` 支持的管理命令。
- Reference only: `.github/workflows/ci.yml` — CI 触发条件、Job、镜像和 Release 资产。
- Reference only: `config.yml.example` — 单实例、多实例配置结构。
- Reference only: `Makefile` — 本地构建命令。
- Reference only: `docs-custom-routes.md`、`docs-custom-outbounds.md`、`docs-dns-providers.md` — 扩展文档链接。
- Reference only: `docs/superpowers/specs/2026-08-30-readme-release-workflow-design.md` — 已确认的设计规格和验收标准。

本次不拆分或新增其他用户文档。README 是唯一需要修改的项目文件，避免增加重复维护入口。

### Task 1: 用已确认的中文结构重写 README

**Files:**
- Modify: `README.md:1-70`
- Reference: `docs/superpowers/specs/2026-08-30-readme-release-workflow-design.md`

- [ ] **Step 1: 记录当前 README 的基线问题**

Run:

```powershell
rg -n '^## |^### |^```' README.md
```

Expected: 输出显示现有 README 只有简短英文结构，并且 `Installer (Linux systemd)` 代码围栏在 `## xbctl` 前没有闭合。

- [ ] **Step 2: 将 README 完整替换为以下内容**

````markdown
# Xboard-Node

[![CI](https://github.com/bennettfrances/Xboard-Node/actions/workflows/ci.yml/badge.svg?branch=dev)](https://github.com/bennettfrances/Xboard-Node/actions/workflows/ci.yml)
[![Dev Release](https://img.shields.io/badge/release-dev-orange)](https://github.com/bennettfrances/Xboard-Node/releases/tag/dev)
[![Go](https://img.shields.io/badge/Go-1.26-00ADD8?logo=go&logoColor=white)](https://go.dev/)
[![License](https://img.shields.io/badge/license-MPL--2.0-blue)](https://www.mozilla.org/MPL/2.0/)

[快速安装](#快速安装) · [安装后管理](#安装后管理) · [维护者发布流程](#维护者发布流程) · [故障排查](#故障排查)

Xboard-Node 是 [Xboard](https://github.com/cedar2025/Xboard) 的节点后端，内嵌 `sing-box` 和 `xray-core` 双内核，可使用单节点、机器和多实例等方式连接面板。

本仓库维护可直接安装的 fork，默认安装通道为 `dev`，对应二进制和 Docker 镜像由本仓库的 GitHub Actions 构建发布。

> **免责声明：** 本项目仅供学习与技术研究使用。使用者应自行确认所在地法律法规及服务条款，并承担使用风险。

## 主要特性

- 支持 V2Ray 系列、Trojan、Shadowsocks、Hysteria2、TUIC、AnyTLS 等协议。
- 内嵌 `sing-box` 与 `xray-core`，可按节点配置选择内核。
- 同时支持 WebSocket 推送和 REST 轮询。
- 支持用户限速、设备限制、在线 IP 跟踪和配置热更新。
- 支持 Node 模式、Machine 模式、独立配置模式和多实例运行。
- 提供 `xbctl`，用于状态、日志、服务、绑定、升级和卸载管理。
- 提供本地健康检查接口，安装器默认使用 `127.0.0.1:65530/healthz`。

## 运行要求

使用 Linux systemd 安装器时，需要：

- 一台运行 systemd 的 Linux 服务器。
- `root` 用户或 `sudo` 权限。
- `amd64`（`x86_64`）或 `arm64`（`aarch64`）架构。
- 可访问 GitHub、面板地址和系统软件源的网络。
- 面板地址、Token，以及 Machine ID 或 Node ID。

安装器正式处理 Ubuntu、Debian、CentOS、RHEL、Rocky Linux、AlmaLinux 和 Fedora；其他发行版仅按尽力支持方式继续安装。

## 快速安装

安装命令中的尖括号内容必须替换为你自己的值。不要把真实 Token 提交到 Git 仓库、Issue 或公开日志中。

### Machine 模式

一台服务器由面板统一管理多个节点时使用：

```bash
curl -fsSL https://raw.githubusercontent.com/bennettfrances/Xboard-Node/dev/install.sh | sudo bash -s -- --mode machine --panel '<PANEL_URL>' --token '<TOKEN>' --machine-id <MACHINE_ID>
```

### Node 模式

一台服务绑定一个面板节点时使用：

```bash
curl -fsSL https://raw.githubusercontent.com/bennettfrances/Xboard-Node/dev/install.sh | sudo bash -s -- --mode node --panel '<PANEL_URL>' --token '<TOKEN>' --node-id <NODE_ID>
```

安装完成后检查：

```bash
sudo xbctl status
sudo xbctl health
```

## 其他部署方式

### Docker

以下环境变量方式适用于 Node 模式：

```bash
docker run -d \
  --name xboard-node \
  --restart=always \
  --network=host \
  -e apiHost='<PANEL_URL>' \
  -e apiKey='<TOKEN>' \
  -e nodeID='<NODE_ID>' \
  ghcr.io/bennettfrances/xboard-node:latest
```

### Docker Compose

`compose` 分支提供 Compose 模板：

```bash
git clone -b compose --depth 1 https://github.com/bennettfrances/Xboard-Node.git xboard-node
cd xboard-node
```

该分支当前模板的 `compose.yml` 默认引用 `cedar2025` 上游镜像。使用本 fork 时，启动前将镜像行改为：

```yaml
image: ghcr.io/bennettfrances/xboard-node:latest
```

随后编辑 `config/config.yml` 中的面板配置并启动：

```bash
docker compose up -d
docker compose logs -f
```

### 本地构建并运行

需要 Go 1.26：

```bash
git clone https://github.com/bennettfrances/Xboard-Node.git
cd Xboard-Node
make build
cp config.yml.example config.yml
```

编辑 `config.yml` 后运行：

```bash
./xboard-node -c ./config.yml
```

## 安装后管理

### 状态、实例和健康检查

```bash
sudo xbctl status
sudo xbctl list
sudo xbctl instance list --output json
sudo xbctl instance get <INSTANCE_ID> --output json
sudo xbctl health
```

### systemd 服务和日志

```bash
sudo xbctl service status
sudo xbctl service restart
sudo xbctl service logs -f
```

`xbctl service` 还支持 `start`、`stop`、`enable` 和 `disable`。

### 添加或删除绑定

```bash
sudo xbctl bind add-node --panel-url '<PANEL_URL>' --token '<TOKEN>' --node-id <NODE_ID>
sudo xbctl bind add-machine --panel-url '<PANEL_URL>' --token '<TOKEN>' --machine-id <MACHINE_ID>
sudo xbctl bind remove-node --panel '<PANEL_URL>' --node-id <NODE_ID>
sudo xbctl bind remove-machine --panel '<PANEL_URL>' --machine-id <MACHINE_ID>
```

也可以按实例 ID 删除：

```bash
sudo xbctl bind remove <INSTANCE_ID>
```

绑定变更会由 `xbctl` 重启服务以加载配置；删除最后一个绑定后服务会停止。

## 配置与多实例

systemd 安装器默认使用以下路径：

| 内容 | 路径 |
| --- | --- |
| 主程序 | `/usr/local/bin/xboard-node` |
| 管理工具 | `/usr/local/bin/xbctl` |
| 配置文件 | `/etc/xboard-node/config.yml` |
| 凭据文件 | `/etc/xboard-node/credentials.env` |
| systemd 服务 | `/etc/systemd/system/xboard-node.service` |

单实例和多实例结构参见 [`config.yml.example`](config.yml.example)。多实例配置示例：

```yaml
kernel:
  type: singbox
log:
  level: info
instances:
  - panel:
      url: '<PANEL_URL>'
      token: '<NODE_TOKEN>'
      node_id: <NODE_ID>
  - panel:
      url: '<PANEL_URL>'
    machine:
      machine_id: <MACHINE_ID>
      token: '<MACHINE_TOKEN>'
```

旧版单面板配置仍然兼容；通过 `xbctl bind` 追加绑定时会迁移到 `instances` 结构。

## 升级与卸载

### 升级

`xbctl upgrade` 默认升级到本仓库的 `dev` Release：

```bash
sudo xbctl upgrade
```

也可以重新获取安装脚本执行升级：

```bash
curl -fsSL https://raw.githubusercontent.com/bennettfrances/Xboard-Node/dev/install.sh | sudo bash -s -- upgrade
```

指定 `latest` 时会下载 GitHub 标记为 Latest 的 Release，而不是 `dev`：

```bash
sudo xbctl upgrade --version latest
```

### 卸载

保留 `/etc/xboard-node` 配置目录：

```bash
sudo xbctl uninstall
```

连同配置一起删除：

```bash
sudo xbctl uninstall --purge --yes
```

> `--purge` 会删除 `/etc/xboard-node`，执行前请备份需要保留的配置和凭据。

## 维护者发布流程

以下流程用于维护 `bennettfrances/Xboard-Node` 的 `dev` 安装通道。

### 1. 检查 GitHub CLI 权限

```bash
gh auth status
gh auth refresh -h github.com -s repo,workflow
```

### 2. 提交并推送到 dev

只暂存本次需要发布的文件：

```bash
git switch dev
git pull --ff-only origin dev
git status --short
git add <PATHS>
git commit -m '<TYPE>: <SUMMARY>'
git push origin dev
```

推送到 `dev` 会自动触发 `.github/workflows/ci.yml`。需要手动重跑时：

```bash
gh workflow run CI --ref dev --repo bennettfrances/Xboard-Node
```

### 3. 查看 CI 结果

```bash
gh run list --repo bennettfrances/Xboard-Node --workflow CI --branch dev --limit 5
gh run watch <RUN_ID> --repo bennettfrances/Xboard-Node --exit-status
```

应确认以下 Job 成功：

- `Test`
- Linux `amd64` 和 `arm64` 的 `Build`
- `Build & Push Docker Image`
- `Release`

### 4. 核对 dev Release

```bash
gh release view dev --repo bennettfrances/Xboard-Node --json tagName,assets --jq '{tag: .tagName, assets: [.assets[].name]}'
```

`dev` Release 必须包含：

- `xboard-node-linux-amd64`
- `xboard-node-linux-arm64`
- `xbctl-linux-amd64`
- `xbctl-linux-arm64`

### 5. 验证安装通道

在测试用 Linux 服务器使用新生成的 Machine Token 验证：

```bash
curl -fsSL https://raw.githubusercontent.com/bennettfrances/Xboard-Node/dev/install.sh | sudo bash -s -- --mode machine --panel '<PANEL_URL>' --token '<TOKEN>' --machine-id <MACHINE_ID>
sudo xbctl status
sudo xbctl health
```

不要在 README、提交记录、Issue 或 Actions 日志中保存真实 Token。

## 故障排查

### 安装器提示二进制下载失败或返回 404

先确认服务器架构和 `dev` Release 资产：

```bash
uname -m
gh release view dev --repo bennettfrances/Xboard-Node --json assets --jq '.assets[].name'
```

服务器只能是 `x86_64`/`amd64` 或 `aarch64`/`arm64`，且 Release 中必须存在对应架构的 `xboard-node` 和 `xbctl`。

### 服务未启动或节点不可用

```bash
sudo xbctl service status
sudo xbctl service logs -n 100
sudo xbctl health
```

重点检查面板地址、Token、Machine ID 或 Node ID 是否匹配，以及节点需要的端口是否已开放和未被占用。

### 重新运行安装器会怎样

重新执行安装命令时，同一面板和目标生成的实例会被更新；不同 Machine ID 或 Node ID 会追加为新实例。调整部署目标前，先备份配置并检查现有实例：

```bash
sudo cp -a /etc/xboard-node "/etc/xboard-node.backup.$(date +%Y%m%d-%H%M%S)"
sudo xbctl list
```

当前 `--force-reconfigure` 参数不会改变上述处理逻辑，不应把它当作配置保护或覆盖开关。

### GitHub Actions 未自动运行

确认代码已经推送到 `dev`，然后检查或手动触发：

```bash
git branch --show-current
git status --short
gh workflow run CI --ref dev --repo bennettfrances/Xboard-Node
```

如果提示权限不足，重新授权 `workflow` scope：

```bash
gh auth refresh -h github.com -s repo,workflow
```

### Docker Compose 启动了错误的镜像

检查 `compose.yml` 的 `image:`，使用本 fork 时应为：

```text
ghcr.io/bennettfrances/xboard-node:latest
```

## 扩展文档

- [自定义路由](docs-custom-routes.md)
- [自定义出口](docs-custom-outbounds.md)
- [ACME DNS-01 提供商](docs-dns-providers.md)

## 许可证

本项目按 [Mozilla Public License 2.0](https://www.mozilla.org/MPL/2.0/) 授权。
````

- [ ] **Step 3: 检查 README 的一级和二级结构**

Run:

```powershell
rg -n '^# |^## ' README.md
```

Expected: 只有一个 `# Xboard-Node` 一级标题，二级标题依次为“主要特性、运行要求、快速安装、其他部署方式、安装后管理、配置与多实例、升级与卸载、维护者发布流程、故障排查、扩展文档、许可证”。项目简介和状态徽章位于第一个二级标题之前。

### Task 2: 验证格式、命令、链接和安全性

**Files:**
- Verify: `README.md`
- Reference: `install.sh`
- Reference: `cmd/xbctl/main.go`
- Reference: `.github/workflows/ci.yml`
- Reference: `config.yml.example`
- Reference: `Makefile`

- [ ] **Step 1: 验证 Markdown 代码围栏成对出现**

Run:

```powershell
$fences = (Select-String -Path README.md -Pattern '^```').Count
if ($fences % 2 -ne 0) { throw "README code fences are unbalanced: $fences" }
$fences
```

Expected: 输出偶数且命令退出码为 0。

- [ ] **Step 2: 验证 README 使用 fork 的安装源、镜像和 Release 资产**

Run:

```powershell
rg -n 'bennettfrances/Xboard-Node|ghcr.io/bennettfrances/xboard-node|xboard-node-linux-amd64|xboard-node-linux-arm64|xbctl-linux-amd64|xbctl-linux-arm64' README.md
```

Expected: 安装脚本、Docker 镜像、GitHub CLI 仓库参数和四个资产名全部命中；`compose` 分支的上游镜像差异仅作为明确警告出现。

- [ ] **Step 3: 验证安装和 xbctl 命令都由源码支持**

Run:

```powershell
rg -n -- '--mode machine|--mode node|--machine-id|--node-id|upgrade|uninstall|--force-reconfigure' install.sh
rg -n 'FORCE_RECONFIGURE' install.sh
rg -n 'xbctl status|xbctl list|instance list|instance get|service status|service restart|service logs|bind add-node|bind add-machine|bind remove-node|bind remove-machine|xbctl health|xbctl upgrade|xbctl uninstall' README.md
rg -n 'xbctl status|xbctl list|instance list|instance get|service status|service.*restart|service.*logs|bind add-node|bind add-machine|bind remove-node|bind remove-machine|xbctl health|xbctl upgrade|xbctl uninstall' cmd/xbctl/main.go
```

Expected: README 中展示的安装参数和管理命令在 `install.sh` 与 `cmd/xbctl/main.go` 中都有对应实现；同时确认 README 明确说明 `--force-reconfigure` 当前不改变处理逻辑，且 `FORCE_RECONFIGURE` 在 `install.sh` 中只有赋值、没有实际读取。

- [ ] **Step 4: 验证本地文档链接存在**

Run:

```powershell
@('config.yml.example', 'docs-custom-routes.md', 'docs-custom-outbounds.md', 'docs-dns-providers.md') | ForEach-Object { if (-not (Test-Path $_)) { throw "Missing README target: $_" } }
```

Expected: 命令无输出并以退出码 0 结束。

- [ ] **Step 5: 验证没有未完成标记，并确认敏感值都使用占位符**

Run:

```powershell
rg -n 'T[B]D|T[O]DO|F[I]XME|X[X]X' README.md
```

Expected: `rg` 退出码为 1 且没有输出，表示没有未完成标记。

Run:

```powershell
rg -n '<PANEL_URL>|<TOKEN>|<MACHINE_ID>|<NODE_ID>|<INSTANCE_ID>' README.md
```

Expected: 所有涉及面板、Token、机器、节点和实例的操作使用明显占位符；人工复核确认没有真实凭据或生产地址。

- [ ] **Step 6: 验证 Git diff**

Run:

```powershell
git diff --check
git diff -- README.md
git status --short
```

Expected: `git diff --check` 无错误；diff 只显示 README 规范化改动；工作区只包含 `README.md` 和尚未提交的本计划文件。

### Task 3: 提交 README 和实施计划

**Files:**
- Commit: `README.md`
- Commit: `docs/superpowers/plans/2026-08-30-readme-standardization.md`

- [ ] **Step 1: 暂存且只暂存目标文件**

Run:

```powershell
git add -- README.md docs/superpowers/plans/2026-08-30-readme-standardization.md
git status --short
```

Expected: 只有上述两个文件处于 staged 状态；没有无关文件被暂存。

- [ ] **Step 2: 提交文档变更**

Run:

```powershell
git commit -m "docs: standardize Chinese README and release guide"
```

Expected: 新提交包含 README 重构和实施计划，提交成功且没有提交无关文件。

- [ ] **Step 3: 验证功能分支干净**

Run:

```powershell
git status --short
git log -3 --oneline
```

Expected: 工作区为空；最近提交依次包含 README 实施、扩展规格和初始规格提交。

### Task 4: 合并到 dev、推送并验证 GitHub

**Files:**
- Git history only: `codex/readme-release-workflow` → `dev`

- [ ] **Step 1: 获取远端并确认 dev 没有意外分叉**

Run:

```powershell
git fetch origin
git rev-list --left-right --count dev...origin/dev
```

Expected: 输出 `0 0`。若不是 `0 0`，停止合并并先检查远端新提交，不能强制推送。

- [ ] **Step 2: 快进合并功能分支**

Run:

```powershell
git switch dev
git merge --ff-only codex/readme-release-workflow
```

Expected: `dev` 快进到包含规格、计划和 README 的最新提交；不产生合并提交。

- [ ] **Step 3: 推送 dev**

Run:

```powershell
git push origin dev
```

Expected: 推送成功，不使用 `--force`。

- [ ] **Step 4: 等待本次 dev CI 完成**

Run:

```powershell
gh run list --repo bennettfrances/Xboard-Node --workflow CI --branch dev --limit 1
gh run watch <RUN_ID> --repo bennettfrances/Xboard-Node --exit-status
```

Expected: 本次推送产生的 CI 完成且结论为 `success`。

- [ ] **Step 5: 核对远端 README 与 dev Release**

Run:

```powershell
gh api repos/bennettfrances/Xboard-Node/readme?ref=dev --jq '.html_url'
gh release view dev --repo bennettfrances/Xboard-Node --json tagName,assets --jq '{tag: .tagName, assets: [.assets[].name]}'
```

Expected: README URL 指向 `dev` 分支；Release tag 为 `dev`，并包含四个 Linux 资产。

## 完成标准

- `README.md` 符合已确认设计规格中的中文优先结构。
- 安装、管理和发布命令都能在当前脚本或源码中找到依据。
- README 不包含真实 Token、密码或生产地址。
- Markdown 围栏、标题、本地链接和 Git diff 检查全部通过。
- 功能分支以快进方式合并到 `dev` 并正常推送。
- GitHub Actions 成功，远端 README 可读取，`dev` Release 四个资产完整。
