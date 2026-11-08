# project-archive — a Claude skill for projects that outlive one chat

[فارسی](README.fa.md)

Long projects with Claude run across many chats, and every new chat starts blank: decisions you already made, drafts you approved, rules like "never mention X publicly", the exact next step — all gone. This skill keeps all of it in one folder with a fixed shape, so a new chat reads two files and carries on as if it had been there.

**The folder is the memory. The chat is disposable.**

## What it does

```
my-project/
├── 00-start-here.md      handoff: owner, source of truth, standing rules, technical notes,
│                         status table, what's left, next step, suggested opening message
├── README.md             folder map + session history
├── 01-<source-of-truth>/ the one document everything else derives from
├── 02-sources/           snapshots of things before they were changed
├── 03-plans/             one plan per upcoming piece of work
├── 04-sessions/          one log per session (time | step | status, decisions verbatim)
└── 05-…                  output folders: drafts, "-saved" versions, backups
```

- **Session start:** reads only the handoff and README, follows the standing rules, opens a session log, and gets going. If the last chat ended abruptly, it catches the handoff up from that chat's log first.
- **During the session:** logs each step as it happens, records your decisions close to your own words, saves every output, shows you anything public before it's applied, and never deletes — old files are kept as `-previous` or set aside with a `_to_delete_` prefix.
- **Heavy chats:** watches for signs the chat is getting long and tells you once, suggesting a clean stop. After each finished piece it updates the handoff right away, so even a chat that dies without warning leaves a correct next step.
- **Lean handoff:** finished work shrinks to one line in the start-here file, with the detail in the session logs, so starting a new chat stays cheap.
- **Session end** (or when you say the chat is getting long): closes the log, updates the status table and next step, and gives you one sentence to paste into the next chat.
- **New project:** asks only what it can't infer (what, where, which language) and builds the folder from what the chat already contains.

Works in English and Persian (templates for both; folder names follow your language), in the Claude app and in Claude Code. It never stores passwords, keys or tokens.

It grew out of a real project — rebuilding a professional profile over five chats — and `project-archive/references/example.md` shows that folder, anonymized.

## Install

**Claude app (claude.ai, desktop, mobile)**
1. Download this repository and zip the `project-archive` folder (the folder itself, not its contents).
2. In Claude, go to **Customize › Skills**, click **+**, choose **Create skill → Upload a skill**, and upload the ZIP.
3. Code execution and file creation need to be on. On Team and Enterprise plans an owner may need to allow custom skills. Details: [Use skills in Claude](https://support.claude.com/en/articles/12512180-use-skills-in-claude).

**Claude Code**
Copy the `project-archive` folder to `~/.claude/skills/` (all your projects) or to `.claude/skills/` in a repository (that project and your team). Details: [Skills in Claude Code](https://code.claude.com/docs/en/skills).

## Use

Just work. It triggers when you say things like:
- "I want to keep working on this over several chats — save everything in a folder."
- "Connect the folder `~/thesis`, read the start-here file and continue."
- "This chat is getting long, wrap up so I can continue in a new one."

At the end of each session you get the opening message for the next one.

## License

MIT — see [LICENSE](LICENSE).
