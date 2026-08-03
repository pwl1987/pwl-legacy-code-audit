# Changelog

All notable changes to this skill are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Special note: this skill follows **TDD for documentation** (RED-GREEN-REFACTOR). Each version's changes are backed by baseline-vs-skill-loaded test runs.

---

## [v0.1.0-alpha] — 2026-07-31

First public release. Tagged **alpha** because cold-start validation is still in progress.

### Added

- **8 Iron Laws** (`SKILL.md`) — the discipline core:
  - 存疑不修改 / 安全网前置 / 严禁触碰区域 / 修复分级 / 无测试不修改 / 回归风险必评 / 过度修复预警 / 漏洞可利用分级
- **5-phase workflow**: 项目画像 → 安全网 → 维度扫描 → 严重度校准 → 出报告
- **4 reference files**:
  - `checklist.md` — 29 dimensions, project-type filter, 7-field problem template, safety net (incl. no-git fallback)
  - `risk-decision-matrix.md` — ✅ / 🟡 / ⚠️ / 📝 4-level classification, Top 10 template, 6-month crash path template
  - `scope-modes.md` — nano / standard / full scope auto-detection
  - `conflict-protocol.md` — C1-C4 user-conflict handling
- **A+C closed loop**: Phase 4 emits machine-readable **fix-task list** (1:1 with findings) for downstream agents (`jd-fix-agent` / `worker` / SRE) to consume
- **Project-type auto-filter**: backend projects auto-disable UI dimensions (11-23), etc.

### Tested on

- TypeScript / JavaScript
  - Fastify route (1005 lines) — full scope
  - Drizzle ORM schema (673 lines) — Iron Law #3 conflict test
  - CI YAML — Iron Law #3 conflict test
  - Test file (605 lines) — Iron Law #5 + C2 conflict test
  - React component (165 lines) — frontend scope, UI dimensions 11-23

### Known limitations

- **Cross-language coverage**: only TS/JS tested. Python / Go / Rust / Java / mobile / embedded not yet validated
- **Primary language**: Chinese-first content; English users need translation
- **Cold-start validation**: in progress, expect undiscovered rationalization loopholes
- **Single-author tested**: only the original author has used this in real reviews

### Not yet included (in [BACKLOG.md](https://github.com/pwl1987/pwl-legacy-code-audit/issues))

These were proposed but deliberately deferred to avoid over-engineering:

- Tool delegation (semgrep / osv-scanner / gitleaks integration)
- Reinventing-the-wheel detection + library recommendations
- npm package distribution

### Security

No security-relevant changes in this version. The skill contains no executable code; all "actions" are git/CLI commands the agent decides to run when following the skill instructions.

---

## Versioning policy

- **alpha** (`0.x.0-alpha`): pre-validation. Expect breaking changes between minors.
- **beta** (`0.x.0-beta`): cold-start validation complete. Breaking changes rare.
- **stable** (`1.0.0+`): iron laws frozen. Only additive changes to references.

The 8 iron laws are **frozen from v1.0.0** — they will not change without a major version bump, because every consumer's audit discipline depends on them.
