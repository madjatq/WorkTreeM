# 小型私有仓库的 GitHub Actions 方案

版本：v1.1

核查日期：2026-11-06

范围：GitHub Free 的 Actions 额度、PR 测试与构建，以及私有仓库能否强制检查通过后再合并。分析假设：使用标准 GitHub 托管 runner；不涉及大型 runner、应用实现或实际账户账单。

## 官方资料确认

GitHub Free 的私有仓库可使用 Actions。当前计费文档列出的额度为每个计费周期 **12 美元计算额度**、**500 MB artifact 与 GitHub Packages 共用存储**，以及**每仓库 10 GB cache**。计算额度在计费周期开始时重置，使用费用归仓库所有者。[1]

标准 runner 的基础价格如下；分钟数为仅使用该平台、且没有其他用量占用计算额度时的换算值，不能当作所有平台通用的分钟配额。[1][2]

| runner | 每分钟费用 | 12 美元约可运行 |
| --- | ---: | ---: |
| Linux 2 核 x64 | 0.006 美元 | 2,000 分钟 |
| Windows 2 核 x64 | 0.010 美元 | 1,200 分钟 |
| macOS 3 或 4 核 | 0.062 美元 | 193 分钟 |

GitHub 定价页仍使用“每月 2,000 CI/CD 分钟”的摘要；详细计费文档采用 12 美元计算额度，两者按上述 Linux 价格换算一致。每个 job 的不足一分钟部分向上取整。额度耗尽后按实际 runner 费率收费；账户没有有效支付方式时，相关用量会被阻止。[1][2][6]

## PR 测试、构建与 AI 的区别

Actions workflow 可以监听 `pull_request`，默认在 PR 创建、重新打开或更新提交（`synchronize`）时运行。workflow 中的 job 可以执行仓库规定的测试、构建等命令，因此 Free 私有仓库可以获得自动 CI 结果。[3][4]

这些是自动化任务，不代表已启用 AI 代码审查。上述额度描述 Actions 的计算与存储；官方计费文档明确，Copilot code review 会同时消耗 Actions 计算资源与 AI credits，因此不能把 Actions 免费额度当作 AI 审查额度。[1][4]

## 私有仓库的合并约束

官方文档确认：GitHub Free 的分支保护适用于公开仓库；私有仓库的分支保护需要 GitHub Pro、Team 或 Enterprise。分支保护可将状态检查设为合并前的必要条件。[5]

因此，Free 私有仓库可以运行 PR 测试和构建，但不能通过分支保护强制这些检查通过后才允许合并。个人私有仓库需要该约束时，可考虑 Pro；组织私有仓库需采用支持该功能的组织方案，例如 Team。[5]

## 来源与适用边界

以下均为 GitHub 官方来源，访问日期为 2026-11-06。账户额度会受其他共享用量影响；尚未核查用户账户套餐、剩余额度、支付设置或实际 workflow 运行时间。

1. [GitHub Actions billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions)：免费额度、共享资源、超额计费与 Copilot code review 计费说明。
2. [Actions runner pricing](https://docs.github.com/en/billing/reference/actions-runner-pricing)：runner 费率及 job 分钟取整。
3. [Events that trigger workflows](https://docs.github.com/en/actions/reference/workflows-and-actions/events-that-trigger-workflows)：PR 事件及默认触发类型。
4. [Workflow syntax for GitHub Actions](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)：workflow、job 及命令配置。
5. [About protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)：套餐适用范围与必要状态检查。
6. [GitHub plans and pricing](https://github.com/pricing)：Free 套餐的 2,000 CI/CD 分钟摘要。

## 变更记录

- v1.1 — 2026-11-06：改为简明中文，保留核心额度与官方来源，明确 CI 和 AI 审查的区别，删除非必要存储超额费率及 ruleset 细节。
- v1.0 — 2026-11-06：根据 GitHub 当前官方文档建立初始研究记录。