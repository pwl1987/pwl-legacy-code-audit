---
name: pwl-legacy-code-audit
description: "Use when reviewing legacy or production code where the original author is unavailable, the change history is murky, or modifications risk silently breaking long-stable features. Triggers include 'code audit', 'security review', 'legacy code review', 'is this safe to change', brownfield pre-go-live checks, and technical-debt scoring. Activate when encountering `as any` casts, ponytail comments, fire-and-forget patterns, god files lacking tests, or modules whose sole maintainer has left."
---

# PWL Brownfield Legacy Code Audit

## Overview

对存量代码做安全审查的唯一原则：**存疑即不动**。宁可不出报告，不可错改一行。任何"看似缺陷"的代码都可能是历史补丁，无 100% 根因确认前，禁止删除、禁止重构、禁止推断业务意图。

## Iron Laws（违反任意一条则报告作废）

| # | 规则 | 边界 |
|---|---|---|
| 1 | **存疑不修改** | 严禁无证据删除/重构/推断意图 |
| 2 | **安全网前置** | 会话开始前必建 `pre-review-snapshot-YYYYMMDD` 分支 + `git status --porcelain`；无 Git 走压缩备份（详见 checklist.md） |
| 3 | **严禁触碰区域** | DDL/DML、CI/CD、计费/权限/对账、未确认疑似废弃代码 — 仅能标"高风险改动"交人工 |
| 4 | **修复分级** | 自动修复仅限确定性缺陷（空指针/XSS/日志脱敏/资源泄漏/格式化）；其余出草案 + 三要素 |
| 5 | **无测试不修改** | 仅能出草案、插风险日志或建议契约快照测试 |
| 6 | **回归风险必评** | 每条草案必答"破坏哪些现有功能" |
| 7 | **过度修复预警** | 线上稳定运行 ≥ 6 个月且无故障记录的理论风险，**禁标阻塞**（最高"严重"且注明"理论风险"） |
| 8 | **漏洞可利用分级** | 标阻塞前必判可达利用链；无可达链仅"严重" |

## 5 阶段流程（缺一不可）

1. **Phase 0 画像 + 范围判定**：扫描根目录判项目类型/语言/版本/年代 → 输出《项目画像报告》→ 末尾**判定 review-scope**（nano/standard/full 三模式，决定交付物粒度。详见 `references/scope-modes.md`）
2. **Phase 1 安全网**：建快照分支 + 锁基线，**原样贴 git 命令与输出写入审计日志**（禁止改写或概括）→ 无 Git 走压缩 + SHA256（见 checklist.md）
3. **Phase 2 维度扫描**：按画像过滤后维度逐项走（见 `references/checklist.md`）
4. **Phase 3 严重度校准**：每条发现过铁律 #7、#8 二次审视
5. **Phase 4 出报告**：按 scope 输出对应项（详见下表）

## 严重度定义

| 级别 | 含义 | 上限约束 |
|---|---|---|
| **阻塞** | 数据丢失 / 已被利用的漏洞 / 构建阻断 | 必有过验证路径或正在被利用 |
| **严重** | 真实功能/安全/可靠性缺陷 | 理论风险的上限 |
| **一般** | 可用性、可维护性、特定边缘场景 | — |
| **建议** | 微改进 / 风格 / 文档 | — |

## 输出契约（按 scope 决定粒度，但 Phase 1 + 铁律不可省）

本 skill **只审计不修复**。Phase 4 末尾输出「修复任务清单」供下游消费（`jd-fix-agent` / `worker` / 运维），形成 A（审计）→ C（整改）闭环。

| scope | 必交交付物 | 跳过项 |
|---|---|---|
| **full** | 项目画像 + 维度扫描 + 风险决策矩阵 + Top 10 + 6 月崩溃路径 + **修复任务清单** | 无 |
| **standard** | 项目画像 + 维度扫描 + 风险决策矩阵 + **修复任务清单** | Top 10、6 月崩溃路径 |
| **nano** | 5 字段速记表（维度/严重度/位置/影响/建议） | 上面 5 项 |

每条发现的 7 字段问题模板见 `references/checklist.md` 的"问题记录模板"。

### 修复任务清单（machine-readable，A+C 闭环）

Phase 4 末尾必输出。**每条 finding 必转成 1 个 task（1:1 映射）**：

```
| task_id | finding | 文件:行 | 自动化等级 | 三要素状态 | 验证方法 | 状态 |
|---|---|---|---|---|---|---|
| T01 | F-01 SSRF | xxx.ts:206 | 🟡 草案 | 待补 | 监控+单测 | pending |
| T02 | F-03 /mine | xxx.ts:546 | ✅ 自动 | N/A | 现有测试 | pending |
```

- `自动化等级`：对应决策矩阵 ✅🟡⚠️📝
- `三要素状态`：仅 🟡 草案区必填（待补 / 已备）
- `状态`：pending → in_progress → fixed → verified → closed（下游 agent 更新）

**下游消费**：✅ → `jd-fix-agent`/`worker`；🟡 → `worker`（三要素齐后）；⚠️ → 运维/SRE；📝 → 项目 BACKLOG

## User Pushback Protocol（用户要求违反铁律或缩减契约时）

**绝不通过省略交付物来满足催稿**。流程：暴露冲突 → 给"最小合规替代" → 等待确认 → 执行。4 种典型场景的处理话术见 `references/conflict-protocol.md`。

## Rationalization 自检（命中即停手）

| 借口 | 现实 |
|---|---|
| "只读不改，无需建分支" | 铁律 #2：会话开始前必建，挡"顺手改一行"诱惑 |
| "明显是 X 导致的" | 铁律 #1：无历史日志 = 推测，禁据此改 |
| "重构一下就清爽了" | 铁律 #1 + #3：禁推断意图、禁碰禁区 |
| "老代码稳了别标阻塞" | 铁律 #7：先查 git log 确认上线 ≥ 6 月无对应故障 |
| "DDL 加个列不危险" | 铁律 #3：DDL 必走人工 |

## Red Flags（出报告前自检）

- 报告含具体代码 patch —— 除非 ✅ 自动修复区，否则违规
- 所有维度都"已检查" —— 几乎一定漏项，过滤后应少于总数
- 严重度全无"理论风险"标注 —— 缺 #7 二次审视
- 缺崩溃路径 / Top 10（full 模式）—— Phase 4 缺项
- 修改建议涉及 `*.sql` / `.github/workflows/` / 权限代码 —— 铁律 #3 违规
- 审计日志未原样贴 git 命令 —— Phase 1 违规

## 强制外部文件（出报告前必 skim）

- `references/checklist.md` — 29 维度清单 + 项目类型过滤 + 7 字段模板 + 安全网启动清单（含无 Git 兜底）
- `references/risk-decision-matrix.md` — ✅🟡⚠️📝 4 级分类 + Top 10 + 6 个月崩溃路径模板
- `references/scope-modes.md` — nano/standard/full 触发条件与对应交付物
- `references/conflict-protocol.md` — 用户与铁律冲突时的暴露-协商流程
