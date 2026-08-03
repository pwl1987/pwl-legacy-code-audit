# pwl-legacy-code-audit

> A discipline-first skill that turns your AI coding agent into a **malicious quality auditor** for legacy / brownfield code — finds risks, **never auto-refactors**, and emits a machine-readable fix-task list for downstream agents to consume.

> 中文：让 AI 编码助手变成「恶意质量审计官」——专审存量/棕地代码，**绝不自动重构**，输出机器可读的修复任务清单交给下游 agent 执行。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status: Alpha](https://img.shields.io/badge/Status-v0.1.0--alpha-orange)](CHANGELOG.md)
[![Agent Skills Standard](https://img.shields.io/badge/Agent%20Skills-standard-blue)](https://agentskills.io)

---

## What it is / 是什么

A [pi-compatible](https://github.com/agent-instructions/pi) **skill** (also works with any agent harness supporting the [Agent Skills standard](https://agentskills.io)) that enforces discipline when reviewing legacy code where:

- The original author is unavailable
- Change history is murky
- Modifications risk silently breaking long-stable features
- You're doing pre-go-live checks on unfamiliar modules
- You're scoring technical debt

**Core principle:** 存疑即不动 (when in doubt, don't touch). The skill refuses to auto-refactor. It only identifies risks, drafts fixes with mandatory human-verification checklists, and emits a structured task list for separate execution agents to consume.

## Why you might want it / 为什么需要

| Without this skill | With this skill |
|---|---|
| Agent "helpfully" refactors code it doesn't fully understand | Agent refuses to touch code without 100% root-cause confirmation |
| Severity calls are optimistic under time pressure | Forced #7/#8 calibration pass (stable ≥ 6 months? reachable attack chain?) |
| Audit report ends, nothing happens next | Phase 4 emits machine-readable **fix-task list** (1:1 with findings) |
| DDL/CI/permission code gets "improved" | Iron Law #3 hard-forbidden zones — drafts only, never patches |
| "Theoretical risk → mark as blocker" over-warning | Iron Law #7 caps theoretical risks at "severe", never "blocker" |

## Install / 安装

See [INSTALL.md](INSTALL.md) for all options. Quickest:

```bash
git clone https://github.com/pwl1987/pwl-legacy-code-audit.git ~/.pi/agent/skills/pwl-legacy-code-audit
```

Then in any pi session: `/skill:pwl-legacy-code-audit` or just ask "审查 xxx.ts" (review xxx.ts) — the skill auto-loads on matching triggers.

## Usage / 使用

**Trigger phrases that auto-load the skill:**

```
"帮我审查 backend/src/routes/articles.ts"           # review this file
"明天上线 xxx 模块，先做次代码审计"                   # pre-go-live audit
"这个 schema 看起来有重复定义，能改吗"                # can I fix this?
"CI 跑太慢，帮我看看"                                # CI is slow, help me
"这个老接口还能改吗？"                                # is this old API safe to change?
```

**What you get (full scope):**

1. **项目画像报告** (Project Profile) — auto-detected project type, filtered dimension list
2. **维度扫描明细** (Dimension Scan) — 29 dimensions, each finding in 7-field template
3. **风险决策矩阵** (Risk Decision Matrix) — ✅ / 🟡 / ⚠️ / 📝 4-level classification
4. **Top 10 最致命风险** (Top 10 Critical Risks) — with fix-cost vs business-impact
5. **6 个月崩溃路径** (6-Month Crash Path) — if nothing is fixed, what dies first
6. **修复任务清单** (Fix-Task List, machine-readable) — 1:1 with findings, downstream agents consume this

**The closed loop / 闭环:**

```
[审计] pwl-legacy-code-audit  →  report + fix-task list
   ↓
[决策] human  →  pick tasks
   ↓
[整改] jd-fix-agent / worker  →  execute tasks
   ↓
[复核] code-review  →  verify fixes
```

The skill only audits. It never executes fixes — that's `jd-fix-agent` / `worker` / SRE's job, by design.

## Repository structure / 仓库结构

```
pwl-legacy-code-audit/
├── SKILL.md                          # main skill (8 iron laws, 5 phases, output contract)
├── references/
│   ├── checklist.md                  # 29 dimensions + 7-field template + safety net
│   ├── risk-decision-matrix.md       # 4-level classification + Top 10 + crash path
│   ├── scope-modes.md                # nano / standard / full scope decision
│   └── conflict-protocol.md          # C1-C4 user-conflict handling
├── README.md                         # this file
├── INSTALL.md                        # installation options
├── CHANGELOG.md                      # version history
└── LICENSE                           # MIT
```

## The 8 Iron Laws / 八条铁律

| # | Law | Boundary |
|---|---|---|
| 1 | **存疑不修改** (don't modify when in doubt) | no evidence → no edit, no refactor, no intent inference |
| 2 | **安全网前置** (safety net first) | `git checkout -b pre-review-snapshot-YYYYMMDD` BEFORE any review session, even read-only |
| 3 | **严禁触碰区域** (forbidden zones) | DDL/DML, CI/CD, billing/permissions/reconciliation — drafts only |
| 4 | **修复分级** (fix tiering) | auto-fix only for deterministic defects; everything else = draft + 3-element checklist |
| 5 | **无测试不修改** (no test, no edit) | draft-only when target lacks test coverage |
| 6 | **回归风险必评** (regression risk required) | every draft must answer "what could this break" |
| 7 | **过度修复预警** (over-fix warning) | stable ≥ 6 months + no incidents → theoretical risks capped at "severe", never "blocker" |
| 8 | **漏洞可利用分级** (exploitability grading) | "blocker" requires verified reachable attack chain |

## Status & limitations / 状态与限制

**Current version: v0.1.0-alpha** — in cold-start validation.

Honest disclosure:

- ✅ **Tested on**: TypeScript / JavaScript (Fastify routes, Drizzle schema, React components, CI YAML, test files)
- ⚠️ **Not yet tested on**: Python / Go / Rust / Java / mobile / embedded
- ⚠️ **Primary language**: Chinese (skill content is Chinese-first; English users may need translation layer)
- ⚠️ **Cold-start validation in progress** — expect rationalization loopholes; report them via Issues

If you hit a case where the agent finds a new excuse to skip an iron law, **please open an Issue** with the verbatim agent output — that's gold for the next REFACTOR cycle.

## Contributing / 贡献

This skill follows **TDD for documentation** (RED-GREEN-REFACTOR). Before any change:

1. Open an Issue describing the agent failure mode you observed
2. Maintainainers will run a baseline test (RED) to reproduce
3. Skill change proposed only after baseline confirms the failure
4. Re-tested post-change (GREEN), loopholes patched (REFACTOR)

**Do NOT** open PRs adding new iron laws / new dimensions / new reference files without a corresponding RED test — over-engineering this skill is explicitly an anti-pattern (Iron Law #7 applied to itself).

## License / 许可证

[MIT](LICENSE) — use it, fork it, sell it, just don't blame me when your agent refuses to "just refactor this real quick".

## Acknowledgments / 致谢

- Built following the [Writing Skills](https://github.com/agent-instructions/superpowers/blob/main/skills/writing-skills/SKILL.md) TDD methodology
- Compatible with the [Agent Skills standard](https://agentskills.io)
- Inspired by years of watching AI agents "helpfully" break production code

---

**中文用户**：本仓库的 skill 内容以中文为主，README 英文化是为国际曝光。如果你只用中文，看 SKILL.md 和 references/ 目录即可，README 只是入口。
