# Install : Frontend Design Skill Package

## Claude Code (local)

```bash
git clone https://github.com/OpenAEC-Foundation/Frontend-Design-Claude-Skill-Package.git
cp -r Frontend-Design-Claude-Skill-Package/skills/source/ ~/.claude/skills/frontend/
```

Restart Claude Code if running. Skills auto-discover from `~/.claude/skills/`.

## As git submodule (recommended for project-specific use)

```bash
cd your-project
git submodule add https://github.com/OpenAEC-Foundation/Frontend-Design-Claude-Skill-Package.git .claude/skills/frontend
git commit -m "feat: add frontend skills as submodule"
```

## Via npm-agentskills

```bash
npx skills add @openaec/frontend-design-claude-skill-package
```

## Claude.ai (web)

1. Open a Claude.ai project
2. Upload individual `SKILL.md` files from `skills/source/**/` as project knowledge
3. Skills activate based on description triggers

## Verification

After install, ask Claude :

> {{VERIFICATION_PROMPT}}

If skill activates : install successful. If not : check `~/.claude/skills/frontend/` exists and contains category-folders.

## Requirements

- Frontend Design evergreen-2026
- Claude Code (latest)
- {{ADDITIONAL_REQUIREMENTS}}
