---
captured_at: '2026-05-02T12:05:08Z'
source: agent
stage: integrated
tags:
- decisions
- cross-platform
- stdin-support
integrated_at: '2026-05-05T05:04:54Z'
---
决策：PowerShell 脚本不支持 stdin 管道输入

在审查项目时发现 ask_codex.ps1 缺少 stdin 支持，而 bash 版本有这个功能。这会导致 Claude Code 通过管道传递大型任务文本时 PowerShell 脚本失败。

考虑的方案：
1. 添加 $input 自动变量检测（PowerShell 原生方式）
2. 保持现状，文档说明 Windows 用户必须用 -Task 参数
3. 统一两个脚本的接口，都移除 stdin 支持

选择方案 1 的理由：
- 保持跨平台一致性
- PowerShell 的 $input 自动变量天然支持管道
- 不破坏现有用户的使用习惯

潜在风险：
- $input 在交互式和非交互式环境下行为不同
- 需要测试 Claude Code 的具体调用方式
