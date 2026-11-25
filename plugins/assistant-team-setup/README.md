# Assistant team setup

A skill that turns one folder on your computer into a team of persistent assistants for your personal life, your work, or both. A primary member coordinates and is who you talk to. The other members each own a broad domain, such as money or communication. They share one plain-Markdown memory, which they keep up themselves.

## Install

```
/plugin marketplace add nishant-gh/nstack
/plugin install assistant-team-setup@nstack
```

Or copy `skills/assistant-team-setup/SKILL.md` to `~/.claude/skills/assistant-team-setup/SKILL.md`. On claude.ai, upload the same file in your skills settings.

## Use

Ask for it in your own words, for example "set up an assistant team for me". It asks a few questions in one round, proposes names and domains if you have no preference, builds the team's folder, and checks that it works. Ask again later to add, retire or change a member.
