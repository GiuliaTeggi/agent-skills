# agent-skills

Skills for Claude Code and other agents that read the `SKILL.md` format.

| Skill                                                    | What it does                                                                                         |
| -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| [trim-code-comments](skills/trim-code-comments/SKILL.md) | Trims the comments a branch adds: deletes stale, redundant or project-jargon ones, shortens the rest. |

## Install

**As a Claude Code plugin** (gets updates):

```
/plugin marketplace add GiuliaTeggi/agent-skills
/plugin install agent-skills@giuliateggi-agent-skills
```

Skills are then available as `/agent-skills:<skill-name>`, and Claude also picks them up automatically when a task matches.

**By copying** one skill into your personal skills folder:

```bash
git clone https://github.com/GiuliaTeggi/agent-skills.git
cp -r agent-skills/skills/trim-code-comments ~/.claude/skills/
```

Use `.claude/skills/` inside a repo instead to share a skill with everyone working on that repo.

## Adding a skill

Create `skills/<skill-name>/SKILL.md` with `name` and `description` frontmatter, and add a row to the table above. Bump `version` in `.claude-plugin/plugin.json` so plugin users get the update.
