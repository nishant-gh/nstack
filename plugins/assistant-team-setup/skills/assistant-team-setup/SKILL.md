---
name: assistant-team-setup
description: Creates and maintains a local team of persistent assistants (a "fellowship") in Claude Code, for personal life, work or both, with broad domains and shared Markdown memory. Use to set one up or change it.
---

# Assistant team setup

Turn one folder on this computer (the **home**) into a team of persistent assistants that run in Claude Code, for the user's personal life, their work, or both. Build it from scratch around this user's answers, and never invent facts about them.

## What a good team looks like

- A **primary** member coordinates. It's who the user talks to whenever they open Claude Code in the home: it handles quick asks, brings in whichever member owns a domain, gives one answer to asks that span domains, sends asks nobody covers to the closest member (suggesting a new member only when a gap keeps coming up), keeps track of what everyone has open, and is proactive (suggests next steps, points out what the user would want to know, prepares things without being asked).
- Every other **member** owns a broad domain (money, health, communication...), is proactive within it, and passes its suggestions through the primary.
- All members share one plain-Markdown **memory**. Sessions are disposable; memory is the continuity.
- **No upkeep for the user.** Members remember, brief, tidy and follow up on their own, and ask the user only what only the user can decide. Judge every choice by this.

## Fixed rules

These go into every team unchanged:

- Memory MUST NOT hold passwords, OTPs, PINs, or full card, account or ID numbers (last 4 digits at most).
- Sending, posting, paying, booking, deleting, or changing anything outside the computer MUST wait for the user's yes to that specific action.
- Routines MUST NOT send, post or pay; they read, and write memory only.
- Members never widen their own permissions. If the same prompt keeps interrupting, they suggest the exact rule once and add it only after the user's yes.
- Ask before changing anything outside the home (shell profile, scheduler, global settings).
- Work data follows the employer's policy on AI tools and data, which wins over this skill's defaults.

## Setup

1. **Check the environment.** Note the OS, which services (MCP servers) this session reaches, and whether `claude --help` lists `--agent`, `--name` and an `auto` permission mode. Look for an existing team only in the folder the user named, or else the current one (a `CLAUDE.md` with a roster); if there is one, see "Changing a team". Never search anywhere else.

2. **Interview in one round.** Propose a concrete default for everything, so "looks good" is a complete answer: the purpose (personal, work, or both, as one team or two); a theme and names, including the primary's; 5 to 7 broad domains, one of them a knowledge keeper; and, optionally, a few facts about the user and which services and routines they want. Example: a personal ship's crew with Mate as the primary, in `~/mate`, with Lookout (research), Signals (communication), Quartermaster (keeper), Purser (money) and Navigator (plans and travel).

3. **Build the home** at `~/<primary>` in lowercase, so there's one name to remember. If that folder already has files, ask before writing there.

   ```
   CLAUDE.md                     charter
   .gitignore                    .claude/settings.local.json
   .claude/settings.json
   .claude/agents/<member>.md    every member except the primary
   .claude/skills/tidy-memory/SKILL.md
   memory/index.md, memory/log.md, memory/members/<member>.md, topic folders
   ```

   - **Charter.** It loads in every session, so keep it short. It covers: the user and their time zone; the primary's role and voice, written as "unless this session was started as another member, you are <Primary>" (the charter is the primary's prompt, so it has no agent file); proactivity, at most one suggestion per reply; the roster; memory rules, with `@memory/index.md` imported; the habits (remember without being asked, never ask the user to organise memory, save as you go since a session can close any time, brief from the index at session start, follow up); and the fixed rules.
   - **Members.** Leave out `tools` so members inherit everything, services included. For example:

     ```markdown
     ---
     name: purser
     description: Purser, the team's money member. Use for budgets, bills, investments and taxes, and whenever the user addresses Purser.
     ---
     You are Purser, the money hand on Asha's crew. Steady and exact. Quartermaster keeps the records; Navigator handles trip costs.
     Start from your notes in memory/members/purser.md. Suggest a next step when there is one; when Mate brought you in, return it to Mate.
     ```

   - **Memory.** One topic per file, with a small YAML header (`type`, `title`, `description`, plus `state` and `due` for tasks) and dated bullets; re-read a file before editing, since another session may have changed it. `index.md` has one line per file, and task lines carry their state and due date, so the brief can come from the index alone. `log.md` is newest first. Each notes file starts with the member's Open threads.
   - **Tidy skill.** Keeps the index and files in sync, headers valid, done and overdue items marked, duplicates merged, and nothing secret stored.
   - **Permissions.** Run the team in auto mode. Claude Code ignores `"defaultMode": "auto"` in a folder's own settings, so the launcher passes `--permission-mode auto`; setting it as the user's default in `~/.claude/settings.json` applies everywhere, so offer that and change it only after a yes. `ask` rules still prompt in auto mode, which makes them the backstop for the fixed rules. Without auto mode, allow `Read(./**)`, `Edit(./memory/**)` and `WebSearch` instead. Project settings apply only once the user accepts the folder trust prompt.
   - **Settings.** `ask` rules (filled in step 5) and a Stop hook that commits every change at the end of each reply:

     ```json
     {
       "permissions": { "ask": [] },
       "hooks": {
         "Stop": [{ "hooks": [{ "type": "command",
           "command": "cd \"$CLAUDE_PROJECT_DIR\" && git add -A && (git diff --cached --quiet || git commit -q -m \"Auto-save $(date '+%Y-%m-%d %H:%M')\"); exit 0" }] }]
       }
     }
     ```

   - **Git.** `git init` the home (offer to install git if it's missing), set a local commit identity such as `<primary>@localhost` so commits never fail, and commit the scaffold. Add a remote only if the user asks, and only a private one.
   - **Launcher.** A shell function named after the primary (PowerShell on Windows):

     ```sh
     mate() {
       ( cd ~/mate || exit
         if [ -z "$1" ]; then claude --permission-mode auto
         else claude --permission-mode auto --agent "$1" --name "$1"; fi )
     }
     ```

     `claude --resume <member>` finds earlier conversations with that member. Without `--agent`, start the member with a first prompt telling it to follow its agent file.

4. **Seed memory** with only what the user said. Store links to documents, not copies.

5. **Connect services** (optional). Use each service's current official MCP server; don't guess package names. Add it with `claude mcp add` in the default local scope, which keeps keys out of the home and out of git. Put its send and delete tools in `ask` by full name (`mcp__<server>__<tool>`).

6. **Add routines.** A routine is `claude -p` on the OS scheduler (launchd catches up after sleep; cron; Task Scheduler), run from the home, with `--allowedTools` limited to reads plus `Edit` and `Write`. Default: the keeper's nightly tidy. Show the user the schedule entry, install it only after a yes, and note in the owner's notes how to remove it. Routines run only while the computer is on.

7. **Verify until it passes.** The roster matches the agent and notes files, the index matches the memory files, `claude doctor` is clean, `git log` shows the scaffold commit, and a fresh `claude -p "Who are you, and who is on the team?"` answers as the primary. Agent files load at session start, so test in a fresh session. Then tell the user briefly how to start, and that there's nothing to maintain.

## Work teams

- First ask whether the employer allows Claude Code with work data, and which services are approved.
- Recommend a separate team when the computer is employer-managed or work data is restricted. In a combined team, keep work memory under `memory/work/` so it can be removed or handed back.
- Plain role names suit work teams. Posting in shared channels or editing shared docs counts as outward. Routines follow working hours. No personal git remote.

## Changing a team

Read the charter, the agent files and the index, then change only what's asked. Keep the roster, agent files, notes files, index and neighbours' hand-offs in sync. Retiring a member moves its notes to `memory/members/retired/`; delete nothing else. Renaming the primary renames the folder and the launcher, so warn the user first: Claude Code keeps conversation history and local service connections by folder path. Finish with the step 7 checks.
