# nstack

Claude Code plugins and skills for everyday use, in one marketplace.

## Install

From inside Claude Code, add the marketplace once:

```
/plugin marketplace add nishant-gh/nstack
```

Then install any plugin below with `/plugin install <name>@nstack`. Run `/plugin marketplace update nstack` to pick up new ones.

While the repo is private, installing works only for people with access to it.

## Plugins

| Plugin | What it does |
|---|---|
| [assistant-team-setup](plugins/assistant-team-setup) | Turns one folder into a team of persistent assistants for your personal life, work or both, with shared Markdown memory they keep up themselves. |

## Adding a plugin

Put it in `plugins/<name>/`, with a `.claude-plugin/plugin.json` and its `skills/`, `agents/`, `commands/` or `hooks/`. List it in `.claude-plugin/marketplace.json` and in the table above, then check it with `claude plugin validate .`.
