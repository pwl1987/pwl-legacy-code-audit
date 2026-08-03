# Installation / 安装指南

Three ways to install. Pick based on your use case.

## Option 1: Global install via git clone (recommended)

**Best for**: you want this skill available in all your pi sessions.

```bash
# Clone directly into pi's global skills directory
git clone https://github.com/pwl1987/pwl-legacy-code-audit.git ~/.pi/agent/skills/pwl-legacy-code-audit
```

**To update later:**

```bash
cd ~/.pi/agent/skills/pwl-legacy-code-audit
git pull
```

**To uninstall:**

```bash
rm -rf ~/.pi/agent/skills/pwl-legacy-code-audit
```

## Option 2: Clone-then-symlink (keeps the repo separate)

**Best for**: you want to keep all cloned skill repos in one place (e.g. `~/skills/`) and symlink them in.

```bash
# 1. Clone to your skills stash
mkdir -p ~/skills
git clone https://github.com/pwl1987/pwl-legacy-code-audit.git ~/skills/pwl-legacy-code-audit

# 2. Symlink into pi's discovery path
ln -s ~/skills/pwl-legacy-code-audit ~/.pi/agent/skills/pwl-legacy-code-audit

# 3. Update later via the stash
cd ~/skills/pwl-legacy-code-audit && git pull
```

## Option 3: Project-level install (team sharing)

**Best for**: you want the whole team to use this skill on a specific repo. The skill travels with the codebase.

```bash
# From your project root
git clone https://github.com/pwl1987/pwl-legacy-code-audit.git .pi/skills/pwl-legacy-code-audit

# Or use the .agents/skills/ path (works across agent harnesses)
git clone https://github.com/pwl1987/pwl-legacy-code-audit.git .agents/skills/pwl-legacy-code-audit
```

Commit the cloned directory to your project repo. Teammates get the skill automatically when they clone.

**Note**: pi only loads project-level skills after the project is trusted (see pi docs on trust).

## Other agent harnesses (Claude Code, Cursor, etc.)

This skill follows the [Agent Skills standard](https://agentskills.io) and works in any harness that supports it. For Claude Code:

```json
// .pi/settings.json or ~/.pi/settings.json
{
  "skills": [
    "~/.claude/skills",
    "/path/to/pwl-legacy-code-audit"
  ]
}
```

## Verify installation / 验证安装

```bash
# 1. Confirm files are in place
ls ~/.pi/agent/skills/pwl-legacy-code-audit/
# Should show: SKILL.md  references/

# 2. Start a fresh pi session (skills are indexed at startup)
pi

# 3. Try forcing the skill to load
/skill:pwl-legacy-code-audit

# 4. Or trigger it naturally
# Type: "帮我审查某个文件" (review some file)
# The skill should auto-load based on the trigger phrases in its description
```

If the skill doesn't appear, see [README troubleshooting](README.md#status--limitations--状态与限制) or open an Issue.

## Installation FAQ

**Q: Do I need to restart pi after installing?**
A: Yes. pi scans skill locations at startup. New skills won't appear in an already-running session.

**Q: Can I install multiple versions?**
A: No — pi uses the first skill found by name. If you need to test a fork, rename its directory.

**Q: Does this skill call any external tools?**
A: No. It's pure markdown documentation injected into the agent's context. The agent may call `git`, `grep`, `npm audit` etc. as part of following the skill, but the skill itself has zero runtime dependencies.

**Q: How much context does this skill consume?**
A: ~580 words for SKILL.md (loaded on skill activation). The 4 reference files (~1.2K words total) are loaded on-demand by the agent when needed.

**Q: Can I use this without pi?**
A: Yes — any agent harness supporting the Agent Skills standard works. You can also just paste the SKILL.md content into a chat as a system prompt.
