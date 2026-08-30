# 中文 README 规范化与发布维护流程设计

## 目标

将项目根目录 `README.md` 重构为一份中文优先、层级清晰、可以直接用于安装和运维的标准项目说明，同时补充仓库维护者的发布流程。文档内容必须以当前源码、脚本和 GitHub Actions 配置为准，不改变程序、安装脚本或持续集成行为。

## 目标读者

- 首次了解 Xboard-Node、需要判断项目用途和支持范围的用户。
- 需要通过 Node 模式或 Machine 模式安装节点的部署人员。
- 需要使用 `xbctl` 管理服务、升级或排查问题的运维人员。
- 需要维护 `bennettfrances/Xboard-Node` fork、运行 CI 并发布 `dev` 版本的仓库管理员。

## 信息架构

README 按以下顺序组织：

1. **项目标题与状态徽章**：展示 CI、`dev` Release、Go 和许可证状态。
2. **项目简介**：用简短中文说明 Xboard-Node 的用途、运行方式和本 fork 的定位。
3. **主要特性**：保留现有能力，按用户能够理解的方式归类。
4. **运行要求**：列出 Linux、systemd、root 或 sudo 权限，以及 amd64、arm64 架构要求。
5. **快速安装**：优先展示 Machine 模式，再展示 Node 模式；使用通用占位符，不出现真实凭据。
6. **其他部署方式**：整理 Docker、Docker Compose 和本地二进制运行方式。
7. **安装后管理**：说明常用 `xbctl` 状态、服务、日志、绑定和实例管理命令。
8. **配置与多实例**：说明配置文件入口、示例文件和多实例使用范围。
9. **升级与卸载**：给出安装脚本与 `xbctl` 当前实际支持的升级、卸载方式。
10. **维护者发布流程**：说明提交推送、自动或手动运行 CI、查看结果、核对 Release 资产和验证安装命令。
11. **故障排查**：覆盖安装、GitHub Actions、Release 资产和服务状态等高频问题。
12. **扩展文档与许可证**：保留自定义路由、自定义出口和 DNS 文档入口，并清楚说明项目许可；免责声明紧接项目简介展示。

## 内容依据与命令约束

README 中的事实和命令只从以下项目文件提取：

- `.github/workflows/ci.yml`：CI 触发条件、构建架构、镜像、`dev` Release 和资产名称。
- `install.sh`：安装、升级、卸载、状态命令，Node/Machine 模式参数及默认发布通道。
- `cmd/xbctl/main.go`：`xbctl` 当前支持的服务、日志、绑定、实例、健康检查、升级和卸载命令。
- `config.yml.example`：配置文件字段和示例。
- `Makefile`：本地构建和 Docker 相关入口。
- `docs-custom-routes.md`、`docs-custom-outbounds.md`、`docs-dns-providers.md`：扩展能力说明。

具体约束如下：

- 安装脚本统一使用 `https://raw.githubusercontent.com/bennettfrances/Xboard-Node/dev/install.sh`。
- 快速安装中的 Machine 模式使用 `--mode machine`、`--panel`、`--token`、`--machine-id`。
- Node 模式只展示源码真实支持的参数，不创造新的选项。
- Docker 镜像使用 `ghcr.io/bennettfrances/xboard-node:latest`。
- `dev` Release 指向当前 fork，并明确核对以下四个 Linux 资产：
  - `xboard-node-linux-amd64`
  - `xboard-node-linux-arm64`
  - `xbctl-linux-amd64`
  - `xbctl-linux-arm64`
- 不把只在排查过程中使用的临时命令写成正式操作步骤。

## 维护者发布流程设计

发布流程采用“准备、触发、观察、核对、验证”五步：

1. **准备**：确认工作区和分支，GitHub CLI 已登录且具有 `repo`、`workflow` 权限。
2. **触发**：将目标改动提交并推送到 `dev`；说明推送会触发 CI，同时提供手动运行工作流的命令。
3. **观察**：查看 GitHub Actions 运行状态，要求测试、Linux amd64/arm64 构建、Release 和 Docker 多架构发布成功。
4. **核对**：检查 `dev` Release 存在，并验证四个资产名称完整。
5. **验证**：使用通用 `PANEL_URL`、`TOKEN`、`MACHINE_ID` 占位符执行 Machine 模式安装验证。

发布说明不包含强制推送，不修改分支保护，也不暗示 CI 成功可以替代实际安装验证。

## 安全与隐私

- 不写入真实面板 Token、GitHub Token、邮箱、密码、服务器地址或其他凭据。
- 所有安装命令使用 `<PANEL_URL>`、`<TOKEN>`、`<MACHINE_ID>`、`<NODE_ID>` 等明显占位符。
- 明确提醒用户不要把真实 Token 提交到 Git 仓库、Issue 或日志中。
- 示例不关闭系统安全功能，也不加入与项目无关的提权或破坏性命令。

## 写作与格式规范

- 中文为主，保留协议名、命令名、文件名和 GitHub 固有术语的英文写法。
- 标题层级连续，避免只有一个子项的空洞章节。
- 第一屏能够看到项目用途、状态和快速安装入口。
- 命令按用途分组，代码围栏必须注明语言并完整闭合。
- 同一事实只在一个主要章节详细说明，其他位置通过链接或短句指向，避免重复维护。
- 不使用未经验证的宣传性描述，不承诺源码没有提供的兼容性或稳定性。

## 验证方式

- 完整阅读重构后的 README，确认标题层级、代码围栏、表格和列表渲染逻辑正确。
- 运行 `git diff --check`，确认没有空白错误。
- 搜索 `TBD`、`TODO`、`FIXME`、`XXX`，确保没有未完成占位内容。
- 对照 `.github/workflows/ci.yml` 检查工作流名称、触发方式、镜像地址和四个 Release 资产。
- 对照 `install.sh`、`cmd/xbctl/main.go`、`config.yml.example` 检查所有正式命令和参数真实存在。
- 检查仓库、工作流、Release、许可证和扩展文档链接的目标正确。
- 检查示例中不存在真实 Token、密码或生产服务器凭据。

## 验收标准

- README 为中文优先，并采用上述 12 个部分的清晰结构。
- 新用户无需阅读源码即可找到 Machine 模式、Node 模式和其他部署方式。
- 运维人员能够找到服务状态、日志、升级、卸载和多实例相关入口。
- 维护者仅阅读 README 即可完成 CI 触发、状态检查、`dev` Release 核对和安装验证。
- 所有命令、链接、镜像和资产名称与仓库当前实现一致。
- Markdown 代码围栏正确闭合，没有未完成占位符或敏感信息。
- 除 `README.md`、本设计规格和实施计划外，不修改任何项目文件。

## 非目标

- 不改变 Go 代码、CI 工作流、Docker 配置、安装脚本或程序运行行为。
- 不新增版本号、CHANGELOG、贡献指南或安全策略文件。
- 不重写独立扩展文档，只在 README 中规范整理入口。
- 不记录本次发布排查中的本机路径、临时目录、磁盘清理或个人环境细节。
