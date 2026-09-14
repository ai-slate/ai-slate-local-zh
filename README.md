# 智匣·融智本地版

**智匣·融智（AI Slate）本地版**把多家模型服务商的接入、调度、调用记录、用量与成本查看收进一个可在本机运行的单一程序。下载并校验后启动一个可执行文件，用浏览器打开管理页面即可开始初始化；不需要先安装 Go、Node、Yarn、Make 或外部数据库。

它不只是把请求转发给上游，而是把**服务商接入、两级调度、计价、调用证据、用量分析和备份恢复**连成一条可核对、可维护的工作流。

**[在线体验](https://demo.local.llmslate.com/)** · **[下载本地版](https://github.com/ai-slate/ai-slate-local-zh/releases)**

![智匣·融智整体架构：接入、调度、模型服务、计价、记录与分析，以及本地单机与企业多实例两种部署边界](./docs/images/ai-slate-v5-v3-architecture.svg)

**架构概览：** 一次 AI 调用如何完成统一接入、调度、计价、记录和分析。本地版与企业多实例的部署边界见[架构图与能力边界](#架构图与能力边界)。

## 在线体验与下载本地版

**先在线体验（当前推荐）：** 无需下载或安装，用浏览器直接打开共享演示环境，先看服务商与模型管理、调用记录和用量分析：

<https://demo.local.llmslate.com/>

演示环境的用户名和密码已经预填，打开页面后填写页面上的图片验证码即可登录。

**下载本地版：** 在 GitHub Releases 获取与你的系统匹配的安装包；平台运行数据保存在你指定的本地运行目录：

<https://github.com/ai-slate/ai-slate-local-zh/releases>

> **在线体验是共享演示环境。** 请只输入演示数据：不要填写真实 API Key、真实业务内容、客户数据或个人敏感信息。演示站可以访问，不代表所有功能、所有模型或长期稳定性都已经验证。

---

## 目录

- [在线体验与下载本地版](#在线体验与下载本地版)
- [统一入口支持的模型能力](#统一入口支持的模型能力)
- [开始前准备](#开始前准备)
- [下载与选择平台](#下载与选择平台)
- [校验与解压](#校验与解压)
- [第一次启动](#第一次启动)
- [完成四步初始化](#完成四步初始化)
- [首次验证](#首次验证)
- [从你的程序调用](#从你的程序调用)
- [核心产品界面](#核心产品界面)
- [它解决什么问题](#它解决什么问题)
- [架构图与能力边界](#架构图与能力边界)
- [日常启停、检查与诊断](#日常启停检查与诊断)
- [数据、安全边界与运行目录](#数据安全边界与运行目录)
- [加密备份、恢复与回滚](#加密备份恢复与回滚)
- [升级版本](#升级版本)
- [常见问题](#常见问题)
- [企业接入与功能定制](#企业接入与功能定制)
- [版本核验与许可](#版本核验与许可)

---

## 统一入口支持的模型能力

智匣·融智可以把已经接入并达到可用状态的以下模型能力纳入统一网关。每个模型能否真正调用，取决于你已配置的服务商、密钥、网络、余额和权限，并以一次最小真实调用为准；这不表示已覆盖全部服务商或全部模型。

| 能力 | 统一网关路径 | 说明 |
|---|---|---|
| 文本生成 | `/responses`、`/chat/completions`、`/messages` | 三种独立协议：OpenAI Responses、OpenAI Chat Completions、Anthropic Messages |
| 文本嵌入 | `/embeddings` | 文本向量化 |
| 图像 | `/images/generations`、`/images/edits` | 图像生成与图像编辑 |
| 语音 | `/audio/transcriptions`、`/audio/speech`、`/audio/generations` | 语音识别、文字转语音、音频生成 |
| 视频 | `/videos`、`/invocations/{id}`、`/invocations/{id}/cancel`、`/files/{id}/content` | 视频生成、异步状态查询、取消与结果下载 |

以上路径都挂在同一个网关基址 `/gateway/llm/v1` 下。统一的是：**网关基址、调用 API Key、模型选择、Provider Key 调度、Invocation 生命周期、用量、计价和文件生命周期**。

不同模态继续使用各自明确的路径与参数——这不是一个相同请求格式覆盖所有能力的“万能接口”。文本生成的三条协议字段也不能混用：`/responses`、`/chat/completions` 和 `/messages` 各有自己的请求结构。

---

## 开始前准备

- **匹配的平台包**：用下面的“下载与选择平台”表，选出与操作系统和 CPU 架构一致的包。
- **可写的运行目录**：默认 `$HOME/.slate`；如果该位置不可写，用 `--runtime-dir` 指向一个有写权限的目录。
- **可用的默认端口**：默认监听 `0.0.0.0:18084`；端口被占用时用 `--port` 换一个端口。
- **产品运行 Licence**：与部署 ID 绑定的技术授权凭证，可在首次进入安装向导时在线领取，或从发行方取得后手动导入（详见“完成四步初始化”）。
- **一个可用的服务商账号与密钥**，以及能访问该服务商的网络。

本地版是自包含的单一程序，不需要预装 Go、Node、Yarn、Make 或外部数据库。

---

## 下载与选择平台

### 当前版本

当前版本：**v3.1.0**（安装包和程序内部版本号为 `3.1.0`）。所有正式安装包只通过 GitHub Releases 分发；下面的表和命令用版本变量减少重复硬编码。

<https://github.com/ai-slate/ai-slate-local-zh/releases>

`v3.1.0` 的 Release 包含五个平台包和根 `SHA256SUMS` 校验清单。通常你只需要下载与你的系统匹配的**一个**平台包，以及根 `SHA256SUMS` 用于校验。

| 你的电脑 | 选择目标 | 包名 |
|---|---|---|
| 普通 64 位 Intel/AMD Linux | `linux-amd64` | `ai-slate-local-${VERSION}-linux-amd64.tar.gz` |
| ARM64 Linux（如 ARM 服务器） | `linux-arm64` | `ai-slate-local-${VERSION}-linux-arm64.tar.gz` |
| 64 位 Windows | `windows-amd64` | `ai-slate-local-${VERSION}-windows-amd64.zip` |
| Intel 芯片的 Mac | `darwin-amd64` | `ai-slate-local-${VERSION}-darwin-amd64.tar.gz` |
| Apple 芯片（M 系列）的 Mac | `darwin-arm64` | `ai-slate-local-${VERSION}-darwin-arm64.tar.gz` |

表中的 `${VERSION}` 就是上文的 `3.1.0`；Windows PowerShell 中写作 `$Version`。判断方法：macOS 在“关于本机”中查看芯片类型，Apple 芯片选 `darwin-arm64`，Intel 选 `darwin-amd64`；Linux 用 `uname -m`，输出 `x86_64` 选 `linux-amd64`，输出 `aarch64` / `arm64` 选 `linux-arm64`；Windows 选择 `windows-amd64`。

每个压缩包解压后只有一个顶层目录，目录内固定包含**八个文件**：目标平台的 `slate`（Windows 为 `slate.exe`）、`README.zh-CN.md`、`RELEASE.json`、`SHA256SUMS`、`LICENSE.md`（本软件的专有许可），以及 `start`、`stop`、`upgrade` 三个脚本（Linux/macOS 为 `.sh`，Windows 为 `.cmd`）。包内 `SHA256SUMS` 校验的是**除它自身以外的七个文件**；`LICENSE.md` 也在完整性校验范围内，使用和转移安装包时请一并保留。

---

## 校验与解压

下载完成后，先校验压缩包本身，再解压，最后校验包内文件。请把 `VERSION` 和 `TARGET` 改成你的版本和目标。

**Linux：**

```bash
VERSION=3.1.0
TARGET=linux-amd64            # 按上表替换为你的目标
PKG="ai-slate-local-${VERSION}-${TARGET}.tar.gz"

# 1) 校验下载的压缩包
sha256sum "$PKG"
# 将输出与同一版本 Release 的根 SHA256SUMS 中同名文件的一行比较

# 2) 解压
tar -xzf "$PKG"
cd "ai-slate-local-${VERSION}-${TARGET}"

# 3) 校验包内除 SHA256SUMS 自身外的七个文件
sha256sum -c SHA256SUMS
```

**macOS：**

```bash
VERSION=3.1.0
TARGET=darwin-arm64           # Intel Mac 改为 darwin-amd64
PKG="ai-slate-local-${VERSION}-${TARGET}.tar.gz"

# 1) 校验下载的压缩包（macOS 自带 shasum，没有 sha256sum）
shasum -a 256 "$PKG"
# 将输出与同一版本 Release 的根 SHA256SUMS 中同名文件的一行比较

# 2) 解压
tar -xzf "$PKG"
cd "ai-slate-local-${VERSION}-${TARGET}"

# 3) 校验包内除 SHA256SUMS 自身外的七个文件
shasum -a 256 -c SHA256SUMS
```

**Windows PowerShell：**

```powershell
$Version = '3.1.0'
$Target  = 'windows-amd64'
$Package = "ai-slate-local-$Version-$Target.zip"

# 1) 校验下载的压缩包
Get-FileHash ".\$Package" -Algorithm SHA256
# 将 Hash 与同一版本 Release 的根 SHA256SUMS 中同名文件的一行比较

# 2) 解压并进入目录
Expand-Archive ".\$Package" -DestinationPath .
Set-Location ".\ai-slate-local-$Version-$Target"

# 3) 逐文件校验包内除 SHA256SUMS 自身外的七个文件
Get-Content .\SHA256SUMS | ForEach-Object {
  $parts = $_ -split '  ', 2
  if ((Get-FileHash -LiteralPath $parts[1] -Algorithm SHA256).Hash.ToLower() -ne $parts[0]) { throw "校验失败：$($parts[1])" }
}
"全部文件校验通过"
```

校验失败时不要继续运行，请重新下载或联系发行方。

---

## 第一次启动

进入解压后的目录，先自检，再启动。

**Linux / macOS：**

```bash
chmod 0755 ./slate ./start.sh ./stop.sh ./upgrade.sh
./slate self-check
./start.sh
# 等价写法：./slate start
```

**Windows PowerShell / CMD：**

```powershell
.\slate.exe self-check
.\start.cmd
# 等价写法：.\slate.exe start
```

`self-check` 会检查内嵌资源、产品模式和许可证公钥，确认文件完整后才会继续。

`start` 默认在后台运行，默认运行目录为 `$HOME/.slate`，默认监听 `0.0.0.0:18084`。启动后直接打开下面的管理页面地址；首次使用会自动进入安装向导，无需特殊链接、Cookie 或额外授权。服务已经运行时再次执行启动命令不会重复启动，也不会清空数据。

启动后访问（使用自定义地址时以启动提示为准）：

- 管理页面与安装向导：<http://127.0.0.1:18084/>
- Swagger API 文档：<http://127.0.0.1:18084/swagger/index.html>
- OpenAPI 文档：<http://127.0.0.1:18084/openapi.json>

需要自定义运行目录、监听地址或端口时，在第一次启动前指定：

```bash
./slate start ./runtime --host 0.0.0.0 --port 18084
# 等价写法：./slate start --runtime-dir ./runtime --host 0.0.0.0 --port 18084
```

> **运行目录一旦投入使用，后续所有命令都必须指向同一个目录。** 不要同时启动两个指向同一运行目录的进程。运行时可通过 `--foreground` 在终端前台观察日志，通过 `--no-open` 禁止自动打开浏览器。不要同时使用位置参数和 `--runtime-dir`。

---

## 完成四步初始化

用任意浏览器打开普通访问地址（程序会在交互终端尝试自动打开浏览器；没有自动打开时手动访问即可）。首次使用会进入安装向导，依次完成四步：

1. **验证许可证**：页面会显示部署 ID（Deployment ID）。支持自动领取的安装包可输入图片验证码领取许可证，使用者名称选填、只作备注。自动领取失败、网络不可用或没有自动领取入口时，复制“部署 ID”发送给发行方，收到许可证后点击“手动导入”，上传文件或粘贴完整内容即可继续。手动导入仍会在本机校验签名、部署绑定和有效期。此步不会创建管理员，也不会保存许可证。
2. **创建管理员**：设置用户名、显示名称和密码，并确认对外 API 地址、API 密钥数量上限与内容保存设置。保存成功后自动建立本次管理员登录态。
3. **接入模型服务**：选择服务商、接入方式与模型，填写服务商密钥。内置目录模型默认全选，可以调整、手动补充或添加多个服务；也可以选择稍后配置。
4. **完成**：查看实际配置结果后进入平台。未接入服务商时安装仍可完成，但接入模型前不能调用。

关于初始化的边界：

- 服务商配置失败不会撤销已创建的管理员或其他已保存服务。刷新、网络中断或未获得登录态时，请使用已创建的管理员正常登录后继续；重复提交安装请求不会重新发放 Token 或覆盖管理员。
- 未保存的密码和服务商密钥在刷新后需要重新输入，不会作为向导草稿持久保存。
- 首次管理员创建前，请仅在可信网络开放服务，避免其他可访问服务的人先行完成初始化。
- 许可证签发私钥只应由发行方保存，安装包内不会包含私钥。许可证到期或不匹配时，普通业务和新的模型调用会暂停，但管理页面、登录、Swagger、产品信息和许可证更换入口仍然可用。
- 恢复备份后部署 ID 保持不变，不要自行生成另一个部署 ID。

---

## 首次验证

初始化完成后，建议按下面的顺序做一次最小验证，确认从界面到调用的链路真实可用：

1. 在管理页面的服务商/模型配置中，对已接入的服务商执行一次**在线验证或最小真实调用**，确认密钥、网络、余额和权限都可用。
2. 打开**调用记录**，找到刚才这次请求，确认它记录了服务商、模型、使用的密钥、真实尝试、上游返回用量、费用明细和错误信息（如有）。
3. 打开**用量与分析**，确认这次调用的次数、计量项和金额已经进入统计，并且可以按用户、API Key、模型等维度查看。
4. 在终端核对实际运行的构建身份：

```bash
./slate version --json   # Windows：.\slate.exe version --json
```

把输出与对应版本下载包的 `RELEASE.json` 对照版本、目标平台和 Bundle 摘要；文件 SHA-256 用平台校验命令与该包记录比较。

> 界面中的“已配置”只表示配置已保存，不代表该账户的全部模型都已通过真实调用验证。目录条目同样只是候选，实际可用性以一次最小真实调用为准。

---

## 从你的程序调用

在管理页面的 **API Key 管理**中创建一个“调用 API Key”（页面只展示掩码，点击复制后完整值会写入剪贴板）。然后把网关基址和这个 Key 配到环境变量里：

```bash
export AI_SLATE_BASE_URL="http://127.0.0.1:18084/gateway/llm/v1"
export AI_SLATE_API_KEY="<你的调用 API Key>"
```

先确认 Key 可用，并查看当前可调用的能力：

```bash
curl -sS "$AI_SLATE_BASE_URL/capabilities" \
  -H "Authorization: Bearer $AI_SLATE_API_KEY"
```

在返回列表里选择一个 `readiness.ready=true` 的模型，再发起一次最小文本请求（把 `your-chat-model` 换成上一步选出的模型代码）：

```bash
curl -sS "$AI_SLATE_BASE_URL/responses" \
  -H "Authorization: Bearer $AI_SLATE_API_KEY" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: first-call-0001" \
  -d '{"model":"your-chat-model","input":"请只回复：接入成功","stream":false}'
```

上面的 Key 和模型都是占位示例，请不要把真实秘密写进脚本或提交到 Git。Chat Completions、Anthropic Messages 以及图像、语音、视频的字段、响应和错误处理，见管理页面内的“接入文档”（Swagger 也提供对应接口说明）。

---

## 核心产品界面

以下三张截图依次展示“配置模型 → 查看全局用量 → 按维度分析”的日常使用路径。截图中的模型、用量和费用数值用于说明页面结构与统计口径，不构成模型可用性、价格或性能承诺。

### 统一管理服务商模型

![智匣·融智服务商模型页面，展示模型能力、上下文、服务商成本、平台售价和状态](./docs/images/screenshots/01-provider-model.jpg)

**图注：** 在一个服务商下集中查看模型能力、上下文窗口、服务商成本、平台售价与启停状态，减少模型配置和价格维护时的信息切换。

### 快速掌握整体用量

![智匣·融智用量统计页面，展示费用、请求次数、Token、多模态指标和月度趋势](./docs/images/screenshots/02-usage-statistics.jpg)

**图注：** 用量统计汇总当月费用、请求次数、Token 与多模态指标，并通过月度趋势快速发现用量变化。

### 按模型、API Key 和用户继续分析

![智匣·融智用量分析页面，按模型展示 Token、输入输出、缓存命中、请求次数和费用](./docs/images/screenshots/03-usage-analysis.jpg)

**图注：** 用量分析可在模型、接入 API Key 和用户三个视角之间切换，结合时间与筛选条件查看 Token、缓存命中、请求次数和费用，并支持导出。

---

## 它解决什么问题

当项目开始使用多个模型服务商时，接口地址、认证字段、密钥、模型 ID、失败记录和费用信息很容易散落在业务代码、脚本、表格和日志里。每增加一个模型，接入、重试、计费、排错和记录就可能重做一遍。

它面向正在评估或准备使用本地 AI 模型服务中枢的中文用户、独立开发者与小团队：当你开始同时使用多家服务商、多把密钥、多种模型能力，并需要查清“这笔调用花了多少、失败在哪一步、数据放在哪里”时，它提供一个统一、可恢复的管理入口。

- 如果只是一个低调用量的个人项目，只接一家服务商，直接使用服务商官方 SDK 或一个成熟网关可能更简单。
- 当第二个服务商、第二把密钥、第一个真实用户或第一次费用追问出现，统一中枢的价值才会真正体现。

智匣·融智本地版把这项工作收进一个产品化入口。以下能力都能在同一个管理页面里完成，并且由可核对的运行机制支撑，而不是概念包装：

- **统一接入**：应用只需要接入一次；服务商、模型、密钥、调度、费用和调用记录由中枢统一管理。文本对话之外，Embedding、语音、图像、视频等不同能力也可以纳入同一套接入体系。
- **可视化管理**：添加服务商、保存密钥、初始化模型、编排模型组合、设置定价、查看调用记录与用量，都在同一套界面完成，不需要在配置文件、数据库和多个零散工具之间来回切换。
- **两级调度与故障边界**：一次调用先选择服务商模型，再从该模型绑定的密钥中选择一把，两层可以分别设置权重。某把密钥欠费时可以换到同模型的其他密钥；某个模型或整家服务商异常时可以绕开对应候选。流式调用只有在首个上游内容到达之前、且能确认请求未发出或被明确拒绝时才会切换；一旦结果可能已被用户看到，就不再重发，宁可让这次失败被看见，也不制造结果未知的重复调用与重复扣费。
- **单次调用证据**：每次请求被记成一条可对齐的账——调用、真实发生的每一次尝试、上游返回的原始用量和对应费用明细。调用了谁、用了哪把密钥、消耗了多少、为什么扣费、在哪一步失败，都能在同一条记录里查到。
- **成本分层与分析**：服务商成本、平台售价、用户实际扣费三层分开记录，各有口径；定价可以在调用发生时冻结，之后改价、重启或跨时段都不会重算已发生的账。在这套账之上，用量可按用户、API Key、模型、任务和时间观察，并支持导出。
- **本地数据与恢复能力**：平台的全部可变运行数据集中在启动时选择的运行目录，支持自检、诊断包、加密备份、备份校验、原地恢复、回滚收尾和从新解压目录升级。调用外部模型时，请求仍会发送给你配置的模型服务商，相关数据处理同时受该服务商规则约束。

> 内置服务商目录中的条目只是尚未绑定凭证的候选，不代表账号已经开通或模型一定可用。能否调用取决于你的密钥、网络、余额和权限，并以一次最小真实调用为准。界面里显示“已配置”也不等于该账户的全部模型都已通过真实调用验证。

---

## 架构图与能力边界

首屏架构图同时表达两种部署形态，请分别理解边界：

- **本地版是当前的一体化单机运行形态**：一个可执行文件内嵌管理页面、API 服务、SQLite 数据能力和运行配置，数据集中在单一运行目录，适合个人与小团队在本机起步。
- **图中企业多实例是目标方向，不是本地版内置开关**：企业形态需要 Go API 多实例与共享 PostgreSQL、Redis、对象存储和独立内容库协同运行。它需以对应产品与部署环境完成验收为准；在真实多节点验收完成之前，不把它称为生产就绪。

---

## 日常启停、检查与诊断

所有命令都必须指向同一个运行目录：

```bash
./slate status   --runtime-dir "$HOME/.slate"
./slate logs     --runtime-dir "$HOME/.slate" --lines 200
./slate restart  --runtime-dir "$HOME/.slate"
./slate diagnose --runtime-dir "$HOME/.slate" --output ./slate-diagnose.zip
./stop.sh        --runtime-dir "$HOME/.slate"
./slate verify   --runtime-dir "$HOME/.slate"
```

Windows PowerShell 使用对应的 .exe / .cmd：

```powershell
.\slate.exe status   --runtime-dir "$HOME\.slate"
.\slate.exe logs     --runtime-dir "$HOME\.slate" --lines 200
.\slate.exe restart  --runtime-dir "$HOME\.slate"
.\slate.exe diagnose --runtime-dir "$HOME\.slate" --output .\slate-diagnose.zip
.\stop.cmd           --runtime-dir "$HOME\.slate"
.\slate.exe verify   --runtime-dir "$HOME\.slate"
```

| 命令 | 作用 |
|---|---|
| `status` | 查看进程、监听地址、运行目录和磁盘占用。 |
| `logs` | 查看最近的中文结构化日志。 |
| `restart` | 复用上一次成功的监听参数重启。 |
| `verify` | 先停止服务，再完整校验 SQLite 和内容存储；运行中执行只校验内嵌资源和目录，数据检查会明确标记为跳过。 |
| `self-check` | 检查内嵌资源、产品模式和许可证公钥。 |
| `version --json` | 查看实际运行文件的构建身份。 |
| `diagnose` | 生成经过脱敏的诊断包；发送前仍应由管理员检查内容。 |
| `help` / `--help` | 查看中文帮助；任意命令后加 `--json` 可输出便于自动化处理的结果。 |

Linux/macOS 也支持把同一个相对目录作为唯一位置参数，例如 `./slate status ./runtime`；从其他工作目录执行时应调整相对路径或改用绝对路径。

> 停止服务不会删除数据。不要通过强制结束进程代替正常 `stop`，除非正常控制命令已经不可用。

---

## 数据、安全边界与运行目录

全部可变数据都位于启动时选择的运行目录（默认 `$HOME/.slate`）：

| 目录 | 用途 |
|---|---|
| `data/current/` | SQLite、缓存、对象和调用内容。 |
| `logs/` | 应用结构化日志与后台进程输出。 |
| `backups/` | 托管的加密备份。 |
| `secrets/` | 当前电脑的登录签名材料等本机秘密。 |
| `generated/` | 启动画像、恢复状态和诊断输出。 |
| `locks/` | 实例锁、维护锁与进程身份。 |
| `tmp/` | 操作级临时文件。 |

请把这份边界放在心上：

- 默认监听 `0.0.0.0` 会允许同一网络中的其他设备访问。**只在可信局域网使用**；对外提供服务时必须配置主机防火墙和 TLS 反向代理。
- 管理员密码、API 密钥、许可证签发私钥和备份 identity 不要写入脚本、聊天记录、日志或诊断包。
- 首次管理员创建前，仅在可信网络开放服务。领取许可证不等于取得登录权限；安装完成后领取和匿名预检入口关闭，普通访问需要正常登录。
- 调用模型时，智匣·融智会把完成该次调用所需的请求内容发送给你配置的模型服务商；请同时检查对应服务商的数据处理、留存和地域规则。
- 支持在线领取许可证的安装包会连接发行方许可证服务；离线环境可以在其他渠道取得与部署 ID 绑定的许可证后手动导入。
- 本地版默认提供“意见反馈”入口。打开反馈面板会为验证码请求发送部署 ID；只有主动提交时，才会发送你填写的反馈类型、正文、可选联系方式，以及部署 ID、产品形态和版本。不会自动上传聊天、调用审计、日志或截图。
- 公钥已经编译进二进制，修改外部文件不能替换许可证信任根。
- 运行目录依赖当前操作系统账号权限；建议同时开启 FileVault、BitLocker 或 LUKS 等磁盘加密。
- 本地版是 **SQLite 单机产品**：不支持把数据库切换成 PostgreSQL，也不支持多个实例共享同一个运行目录。它不读取同目录的 TOML/YAML 配置，也不能通过配置文件切换产品模式。
- 服务端数据请通过内置备份命令迁移，不要在服务运行时直接复制 SQLite、Pebble 或缓存目录。

更完整的数据流说明见 [docs/data-and-privacy.md](./docs/data-and-privacy.md)。

---

## 加密备份、恢复与回滚

### 创建加密备份

自动备份推荐使用独立的 X25519 identity。identity 相当于备份私钥：**丢失后无法恢复，泄露后他人可以解密备份。**

```bash
./slate backup keygen --identity-file ./slate-backup-identity.txt
# 将上一条命令显示的 AGE 公钥单独保存到 recipient.txt
./slate backup create --runtime-dir "$HOME/.slate" --recipient-file ./recipient.txt
./slate backup list   --runtime-dir "$HOME/.slate"
./slate backup verify --runtime-dir "$HOME/.slate" \
  --source "$HOME/.slate/backups/<backup>.tar.gz.age" \
  --identity-file ./slate-backup-identity.txt
```

- 备份文件扩展名为 `.tar.gz.age`。创建完成后应把**备份文件和 identity 分开保存**，并定期执行 `backup verify`。
- `backup inspect` 使用与 `backup verify` 相同的 `--source` 和 `--identity-file` 参数查看解密后的清单。
- 删除不再需要的托管备份：`backup delete --runtime-dir <原运行目录> --id <完整备份文件名> --yes`。
- 也可在创建备份时用 `--passphrase` 替代 `--recipient-file`，在交互式终端隐藏输入口令（至少 12 个字符）；口令不会从命令参数、环境变量或配置读取，忘记后无法找回。
- 备份不包含 JWT 签名密钥、日志、锁、Dashboard 或许可证私钥；诊断包、日志、锁和本机登录签名密钥不会作为业务备份恢复到另一台电脑。

### 原地恢复与回滚

恢复前必须确认源实例已经停止，同一个部署 ID 不得同时在两台机器运行。

```bash
./slate stop    --runtime-dir "$HOME/.slate"
./slate restore --runtime-dir "$HOME/.slate" \
  --source "$HOME/.slate/backups/<backup>.tar.gz.age" \
  --identity-file ./slate-backup-identity.txt \
  --in-place --yes
./slate recovery status --runtime-dir "$HOME/.slate"
./slate start   --runtime-dir "$HOME/.slate"

# 确认数据后先停止服务，再收尾：
./slate stop     --runtime-dir "$HOME/.slate"
./slate recovery finalize --runtime-dir "$HOME/.slate" --yes
./slate start    --runtime-dir "$HOME/.slate"

# 需要回退到恢复前的数据时：
./slate recovery rollback --runtime-dir "$HOME/.slate" --yes
```

原地恢复只切换 `data/current`，稳定保留日志、备份、锁和本机 secrets。确认恢复后的业务正确后先 `stop`，再执行 `recovery finalize`，最后重新 `start`；需要切回旧数据时执行 `recovery rollback`。存在待确认回滚点时，系统会拒绝第二次原地恢复。

需要迁移到新目录做灾备恢复时，先停止原部署，再使用 `--target-runtime-dir <新目录> --disaster-recovery --yes`；新目录会保留部署 ID、许可证、Root 和业务数据，但生成新的本机登录会话签名密钥，旧会话不会复活。

---

## 升级版本

日常升级只需替换二进制并重启。数据库、许可证、服务商密钥和本机登录签名材料继续使用原运行目录。

**关键原则：不要在旧二进制自身上运行自替换命令。** 把新版本解压到**另一个目录**，完成下载校验后，从**新安装包**运行 `upgrade`，`--target` 指向旧安装的二进制，`--runtime-dir` 指向原运行目录。

```bash
# 当前目录为新版本解压目录；target 指向旧安装目录中的二进制
./slate self-check
./upgrade.sh --target "/opt/ai-slate/slate" --runtime-dir "$HOME/.slate" --check
./upgrade.sh --target "/opt/ai-slate/slate" --runtime-dir "$HOME/.slate"
# 可选：在上面命令末尾增加 --backup --recipient-file ./recipient.txt
# 或增加 --backup --passphrase，在终端隐藏输入备份口令
```

**Windows：** 从新包目录执行 `upgrade.cmd --target "C:\AI Slate\slate.exe" --runtime-dir <原运行目录>`。

主要选项：

| 选项 | 行为 |
|---|---|
| `--target` | 必填：旧安装中的二进制文件路径。 |
| `--runtime-dir` | 原运行目录；省略时为当前账号的默认目录。 |
| `--check` | 只预检，不停服、不替换。 |
| `--backup` | 默认否；开启后先用旧版本创建加密业务备份，须同时选择 `--recipient-file` 或 `--passphrase`。 |
| `--no-start` | 替换后保持停止；默认按原来成功启动的地址和端口启动。 |
| `--drain-timeout` | 优雅停服等待上限，默认 `30m`，范围 `1m` 到 `24h`。 |

升级命令会先校验产品、平台、许可证公钥指纹、目标文件身份与空间；停服后独占原运行目录，完成数据和端口检查，再替换文件并验证新进程身份。相同文件摘要再次升级会直接返回成功，不重复停服。默认不生成业务数据备份；替换期间暂时保留的旧二进制用于失败恢复，不是数据备份。

- 断电、磁盘故障等造成升级中断时，命令会保留 `generated/binary-upgrade.json`。下一次升级会拒绝覆盖该记录；请先通过 `status` 确认服务已停止，按记录核对文件，用准备运行的二进制离线 `verify` 成功后再恢复启动。数据已迁移时不得直接回退旧二进制。
- 跨存储版本升级须按发行方的迁移说明执行；不要手工修改数据库版本，也不要把已经迁移的数据交给旧版本。
- 升级只更新目标二进制，不覆盖你修改过的脚本。旧目录内的 `RELEASE.json` 和 `SHA256SUMS` 仍描述原下载包；升级后应以**新下载包**的摘要、`RELEASE.json` 和目标二进制的 `version --json` 为准。

---

## 常见问题

### 页面打不开

先运行 `status`，确认服务正在运行且端口正确；再检查端口占用、防火墙和 `logs`。如果使用了自定义端口，浏览器地址也要同步修改。

### 显示“Licence 缺失、无效或已到期”

复制当前页面显示的 Deployment ID，向发行方申请匹配的 Licence。备份恢复后 Deployment ID 保持不变；不要自行生成另一个 Deployment ID。在线领取失败时仍可用“手动导入”继续。

### 服务启动失败

依次运行 `self-check`、`status`、`verify` 和 `diagnose`。请保留错误原文、产品版本、目标平台和诊断包，再联系发行方；不要发送管理员密码、API 密钥、Licence 私钥或备份 identity。

### 模型已经配置，但调用不可用

配置已保存不等于模型可用。请确认该服务商的密钥、网络、余额和权限，并对该模型执行一次最小真实调用；内置目录条目只是候选。目录未覆盖的服务商可通过定制接入。

### 如何确认版本

运行 `./slate version --json`，并与对应版本下载包的 `RELEASE.json` 对照版本、目标平台和 Bundle digest；文件 SHA-256 使用对应平台的校验命令与该包记录比较。

---

## 企业接入与功能定制

个人与小团队**无需联系作者**，按上面的步骤自助下载和使用即可。以下是有企业级需求时的可选承接。

除个人与小团队在本机使用外，智匣·融智也承接企业内部与垂直行业场景的模型服务接入和功能定制：

- **企业内部 AI 平台或垂直行业应用**：把多家模型服务商、成员使用与成本管理收进统一入口。
- **目录未覆盖的模型服务商**：按目标服务商的真实接口与计量规则接入，而不是只依赖内置目录。
- **业务流程和管理规则接入**：把权限、额度、审批或内部结算等管理规则接到同一套体系中。

定制基于现有的接入、调度、计费、存储和分析体系开发，不另起一套旁路；完成后进入同一管理体系，可以继续使用、升级和维护。合作前会先明确使用场景、交付结果和验收标准，再基于现有体系完成开发。

**联系作者：13065007223（电话 / 微信同号）**

---

## 版本核验与许可

- 当前版本说明：[智匣·融智本地版 v3.1.0](./docs/releases/v3.1.0.md)。
- 安装包与包内文件：使用“校验与解压”中的命令，对照同一版本 Release 的根 `SHA256SUMS`。
- 运行中的真实构建身份：`./slate version --json`（Windows：`.\slate.exe version --json`），并与该版本下载包的 `RELEASE.json` 对照版本、目标平台和 Bundle 摘要。
- 下载入口：<https://github.com/ai-slate/ai-slate-local-zh/releases>。
- 专有软件许可：[LICENSE.md](./LICENSE.md)。
- 支持与问题反馈：[SUPPORT.md](./SUPPORT.md)。
- 安全报告：[SECURITY.md](./SECURITY.md)。
- 数据与隐私：[docs/data-and-privacy.md](./docs/data-and-privacy.md)。
