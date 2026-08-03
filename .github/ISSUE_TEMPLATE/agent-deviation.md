---
name: Agent Deviation Report
about: Report a case where the agent found a new excuse to skip an iron law, skip an output contract item, or otherwise misbehave. This is gold for the next REFACTOR cycle.
title: "[DEVIATION] "
labels: agent-deviation, needs-baseline-test
---

> **Why this template exists**: This skill follows TDD-for-documentation (RED-GREEN-REFACTOR). Every reported deviation becomes a baseline test that informs the next skill revision. Verbatim agent output is more valuable than your summary of it.

## Which iron law / contract item was violated?

Check all that apply:

- [ ] **Iron Law #1** 存疑不修改 — agent edited/refactored code without 100% root-cause confirmation
- [ ] **Iron Law #2** 安全网前置 — agent skipped `pre-review-snapshot-YYYYMMDD` branch creation (incl. "read-only, no snapshot needed" excuse)
- [ ] **Iron Law #3** 严禁触碰区域 — agent touched DDL / CI/CD / billing / permissions / reconciliation code
- [ ] **Iron Law #4** 修复分级 — agent auto-fixed something that should have been a draft
- [ ] **Iron Law #5** 无测试不修改 — agent edited code without test coverage
- [ ] **Iron Law #6** 回归风险必评 — draft missing "what could this break" answer
- [ ] **Iron Law #7** 过度修复预警 — theoretical risk marked as blocker
- [ ] **Iron Law #8** 漏洞可利用分级 — severity misgraded
- [ ] **Phase 1** audit log didn't paste git commands verbatim
- [ ] **Phase 4** fix-task list not 1:1 with findings
- [ ] **Conflict protocol** C1-C4 not triggered when it should have been
- [ ] **Scope mode** nano/standard/full misjudged
- [ ] Other: ________

## Trigger scenario

**Your original words to the agent (verbatim — most important field)**:

> ...

**File under review**:
- [ ] Backend route / handler
- [ ] Frontend component
- [ ] Database schema / migration
- [ ] CI/CD config
- [ ] Test file
- [ ] Other: ________

**Project language**:
- [ ] TypeScript / JavaScript
- [ ] Python
- [ ] Go
- [ ] Rust
- [ ] Java
- [ ] Other: ________

## What the agent actually did (quote, don't summarize)

> ...

## What rationalization / excuse the agent used

> ...

Examples: "用户催得紧，先出报告再说" / "明显是 X 导致的" / "本次只读不改，无需建分支" / "反正重构一下就好了"

## What you expected the agent to do instead

> ...

## Attachment (optional)

Full audit report or relevant snippets. Long outputs can be collapsed in `<details>`.

---

### Maintainer metadata (please keep)

- Skill version: [e.g. v0.1.0-alpha — see CHANGELOG.md]
- pi / agent harness version:
- Reproducible: yes / no / not tried
