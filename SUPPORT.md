# 支持

本页说明遇到问题时的自助检查顺序、提交问题所需的材料，以及不应发送的内容。

## 自助检查顺序

1. 先查 [项目 README](./README.md)，特别是其中的“常见问题”。
2. `./slate self-check`（Windows：`.\slate.exe self-check`）：检查内嵌资源、产品模式和许可证公钥。
3. `./slate status`：查看进程、监听地址、运行目录和磁盘占用。
4. `./slate logs --lines 200`：查看最近的中文结构化日志。
5. `./slate verify`：先停止服务，再完整校验 SQLite 和内容存储。
6. `./slate diagnose --output ./slate-diagnose.zip`：生成经过脱敏的诊断包。

## 提交问题时请提供

- 产品版本与目标平台：`./slate version --json`（Windows：`.\slate.exe version --json`）。
- 复现步骤：你做了什么、期望发生什么、实际发生什么。
- 脱敏后的错误原文（去掉密钥、账号、业务内容和客户信息）。
- 相关时提供下载包的构建身份与 SHA-256，方便核对版本。

## 请勿发送

- 管理员密码
- 调用 API Key
- 服务商（Provider）密钥
- Licence 私钥
- 备份 identity
- 原始客户内容

诊断包在发送前也应由管理员检查内容。

## 联系

13065007223（电话 / 微信同号）。当前未提供固定响应时限；处理优先级会结合问题影响和可复现信息确定。

相关文件：[专有软件许可](./LICENSE.md) · [安全](./SECURITY.md) · [数据与隐私](./docs/data-and-privacy.md)
