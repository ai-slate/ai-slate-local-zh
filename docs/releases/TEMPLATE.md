> **编辑说明（复制本文件后必须删除本段）**
>
> 1. 将本文件复制为 `docs/releases/vX.Y.Z.md`，把标题、正文和链接中的 `vX.Y.Z` 替换为实际版本号；包名中的 `X.Y.Z` 不带 `v` 前缀。
> 2. 填写所有 `<尖括号>` 占位符；本次未用到的可选内容（如“本次更新”中没有的类别）连同小标题整段删除，不要保留空标题。
> 3. “发行签名说明”下的两个段落互斥：按本次实际发行模式**只保留其一**，删除另一个，并把章节标题改为 `## 发行签名说明`。
> 4. 只写本次真实验证过的事实：没有新增、改进或修复的类别直接删除；权限受限、配额不足等如实写成“权限限制”，不要写成模型调用成功；不要把内部构建状态搬进公开正文。
> 5. 本文件的相对链接按存放在 `docs/releases/` 目录设计；若调整存放位置，同步检查文内链接。
> 6. 发布前通读一遍：确认版本号一致、下载资产与实际上传文件名一致、不含任何公司名称与凭证信息，并删除本编辑说明。

# 智匣·融智（AI Slate）vX.Y.Z

发布日期：<发布日期>

智匣·融智（AI Slate）把多家模型服务商的接入、调度、调用记录、用量、计价、文件生命周期和数据恢复收进一个可在本机运行的单一程序，并把这些环节连成一条可核对的闭环。本页帮助你快速了解这个版本更新了什么、应下载哪个文件、如何完成安装或升级，以及遇到问题时去哪里获得支持。

## 版本定位

<vX.Y.Z 一句话定位：本次发布面向谁、在什么场景下带来什么核心价值。例如：“vX.Y.Z 面向正在本机搭建和使用多模型 AI 服务的中文用户、独立开发者与小团队，重点改进了……”>

产品在本地单机形态下提供完整的管理页面与 API 服务：统一管理服务商、模型、服务商密钥、API Key、调度与用量计价；可变运行数据集中在你指定的运行目录。安装包自包含，不需要安装 Go、Node、Yarn、Make 或外部数据库。

## 本次更新

<!-- 逐条填写本次真实交付的内容，说明对使用者的影响；没有的类别删除整行。 -->

- 新增：<本次更新：新增的能力、资产或流程>
- 改进：<本次更新：体验、稳定性或文档方面的改进>
- 修复：<本次更新：修复的问题及其影响范围>

## 统一入口支持的模型能力

通过统一入口管理和调用以下模型能力；统一的是网关基址、调用 API Key、模型选择、调度与用量计量，各能力保留各自明确的请求格式与参数：

- 文本生成：支持 OpenAI Responses、OpenAI Chat Completions、Anthropic Messages 三种协议结构；
- 文本嵌入；
- 图像生成与图像编辑；
- 语音识别、文字转语音与音频生成；
- 视频生成。

所有调用进入同一套调度、计量与审计体系，可在管理页面查看调用记录、用量与分析结果。完整的模型能力说明与最小调用示例见 [项目 README](../../README.md#统一入口支持的模型能力)。

## Agent 接入

支持 Claude Code、Codex、OpenCode 等 Agent 工具接入：Claude Code 使用 Anthropic Messages，Codex 使用 OpenAI Responses，OpenCode 使用 OpenAI-compatible Chat Completions。具体配置与验证步骤见产品管理页面内的“在线接入文档”，按你使用的 Agent 选择对应指引即可。

## 下载资产

本次 Release 提供五个平台包和根 `SHA256SUMS` 校验清单：

| 平台 | 文件 |
|---|---|
| Linux x86_64 | `ai-slate-local-X.Y.Z-linux-amd64.tar.gz` |
| Linux ARM64 | `ai-slate-local-X.Y.Z-linux-arm64.tar.gz` |
| Windows x86_64 | `ai-slate-local-X.Y.Z-windows-amd64.zip` |
| macOS Intel | `ai-slate-local-X.Y.Z-darwin-amd64.tar.gz` |
| macOS Apple Silicon | `ai-slate-local-X.Y.Z-darwin-arm64.tar.gz` |

通常只需下载与你系统匹配的一个平台包，并下载根 `SHA256SUMS` 用于校验。

### 包内内容

每个平台包解压后只有一个顶层目录，固定包含：

- 目标平台程序（Linux/macOS 为 `slate`，Windows 为 `slate.exe`）；
- `README.zh-CN.md`（本地使用说明）；
- `RELEASE.json`（本次构建信息）；
- `SHA256SUMS`（包内文件校验清单）；
- `LICENSE.md`（专有软件许可）；
- `start`、`stop`、`upgrade` 三个脚本（Linux/macOS 为 `.sh`，Windows 为 `.cmd`）。

## 发行签名说明（二选一，保留与本次一致的一段）

**signed（已签名发行）：** 本版本中需要签名的平台安装包已完成本次发行声明的代码签名。按上文校验清单核对完整性后，解压并按“快速开始”正常启动即可；如系统弹出确认提示，确认来源后继续。

**unsigned-public（未签名公开发行）：** 本版本的 macOS 安装包未使用 Developer ID 签名，也没有经过 Apple 公证，首次启动时可能被 Gatekeeper 拦截。请按 [README 的“macOS 首次启动被 Gatekeeper 拦截”](../../README.md#macos-首次启动被-gatekeeper-拦截)中的说明处理；不要删除文件的隔离属性，也不要全局关闭 Gatekeeper。校验 `SHA256SUMS` 可以确认下载文件完整无损坏。

## 快速开始

1. 前往 [GitHub Releases](https://github.com/ai-slate/ai-slate-local-zh/releases)，下载与操作系统和 CPU 架构匹配的平台包，以及根 `SHA256SUMS`。
2. 校验下载文件：对压缩包核对 SHA-256；解压后进入包内顶层目录，再校验除 `SHA256SUMS` 自身外的七个文件。Linux 使用 `sha256sum -c SHA256SUMS`，macOS 使用 `shasum -a 256 -c SHA256SUMS`，Windows 使用 PowerShell 逐文件比对。完整的分平台命令见 [README 的“校验与解压”](../../README.md#校验与解压)。
3. 启动：Linux/macOS 依次执行 `chmod 0755 ./slate ./start.sh ./stop.sh ./upgrade.sh`、`./slate self-check`、`./start.sh`；Windows 依次执行 `.\slate.exe self-check`、`.\start.cmd`。
4. 打开管理页面 <http://127.0.0.1:18084/> 进入安装向导，依次完成：验证许可证、创建管理员、接入模型服务、完成确认。Swagger 文档在 <http://127.0.0.1:18084/swagger/index.html>，接口文档在 <http://127.0.0.1:18084/openapi.json>。
5. 做一次最小真实调用，并在调用记录、用量与分析中核对结果。

## 升级说明

- 从 `v<旧版本号>` 升级到 vX.Y.Z 前，先按 [README 的“创建加密备份”](../../README.md#创建加密备份)完成备份。
- 把新版本解压到**另一个目录**，从新安装包运行升级脚本：用 `--target` 指向旧安装中的二进制，`--runtime-dir` 指向原运行目录；不要在旧二进制自身上执行自替换命令。
- 建议先运行 `./upgrade.sh --target <旧二进制路径> --runtime-dir <原运行目录> --check` 预检，确认无误后去掉 `--check` 执行升级。Windows 使用 `upgrade.cmd`。
- 升级只替换二进制并重启；数据库、许可证、服务商密钥和本机登录签名材料继续使用原运行目录。
- 涉及数据库结构变化的版本，按本页“本次更新”中的迁移说明操作，不要假设数据库可逆，也不要手工修改数据库版本。完整步骤见 [README 的“升级版本”](../../README.md#升级版本)。

## 运行与安全提示

- 日常启停与检查：Linux/macOS 使用 `./start.sh`、`./stop.sh`、`./slate status`、`./slate self-check`；Windows 使用对应的 `.cmd` 脚本与 `.\slate.exe`。
- 数据与日志集中保存在你指定的运行目录；升级、迁移或重装前先创建加密备份。
- 首次初始化前，请在可信网络中开放服务，并由你本人完成管理员创建，避免他人抢先完成初始化。
- 默认监听地址与网络暴露的注意事项，见包内 `README.zh-CN.md` 的“网络与安全提醒”。
- 模型能否调用取决于你已配置的服务商、密钥、网络、余额和权限；账号权限受限时会明确提示为权限限制。实际可用性以一次最小真实调用为准。
- 价格缺失时会明确标记为缺失，不会当作无费用处理。

## 支持、隐私与许可

- 问题反馈与支持：[SUPPORT.md](../../SUPPORT.md)
- 安全问题报告：[SECURITY.md](../../SECURITY.md)
- 数据与隐私：[数据与隐私说明](../data-and-privacy.md)
- 专有软件许可：[LICENSE.md](../../LICENSE.md)。本软件为专有软件，未经事先书面授权，不得复制、再分发、修改、反向工程，或以托管方式向第三方提供软件本身。企业内部 AI 平台、目录未覆盖的模型服务商以及业务流程定制可联系发行方承接；个人与小团队无需联系即可自助使用。

## 了解更多

- 项目 README（产品介绍、首次启动与日常运维）：<https://github.com/ai-slate/ai-slate-local-zh>
- 本版本下载：<https://github.com/ai-slate/ai-slate-local-zh/releases/tag/vX.Y.Z>
- 全部历史版本：<https://github.com/ai-slate/ai-slate-local-zh/releases>
