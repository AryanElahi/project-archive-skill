# project-archive — a Claude skill for projects that outlive one chat

[فارسی](README.fa.md)

Long projects with Claude run across many chats, and every new chat starts blank: decisions you already made, drafts you approved, rules like "never mention X publicly", the exact next step — all gone. This skill keeps all of it in one folder with a fixed shape, so a new chat reads two files and carries on as if it had been there.

**The folder is the memory. The chat is disposable.**

## Architecture

The skill has two parts: the skill itself (instructions Claude reads) and the folder it builds for each project (that project's memory).

### 1. The skill's own files

```
project-archive/
├── SKILL.md              the instructions
├── references/
│   └── example.md        an anonymized, mature archive from a real project
└── templates/
    ├── 00-start-here.md
    ├── README.md
    ├── session-log.md
    └── plan.md
```

They load in three layers, so tokens are only spent when needed:

| Layer | What | When it loads | Approx. cost |
| --- | --- | --- | --- |
| 1 | Name + description | Always, in every chat | ~165 tokens |
| 2 | `SKILL.md` | When the skill triggers, once per chat | ~2,500 tokens |
| 3 | Templates, example | Only when creating a file or bootstrapping a project | ~100–950 tokens each |

### 2. The folder it builds for each project

```
my-project/
├── 00-start-here.md      the handoff — the one file every new chat must read
├── README.md             folder map + session history
├── 01-<source-of-truth>/ the approved document everything else derives from
├── 02-sources/           snapshots of things before they were changed
├── 03-plans/             one plan per upcoming piece of work
├── 04-sessions/          one log per session
└── 05-…                  output folders, created only when there's output
```

**The handoff (`00-start-here.md`)** has eight fixed sections: owner, source of truth, standing rules, technical notes, status table, open items, upcoming sessions, and the suggested opening message for the next chat.

**Each session log** holds the user's opening message verbatim, a `time | step | status` table, the user's decisions, anything left open, and notes.

## Features

**Session lifecycle**
- **Start:** connects the folder, reads only the handoff and README, adopts the standing rules for the whole session, opens a session log, and starts the next step without needless permission-asking. If the plan has open questions, it asks them all at once.
- **Recovery from a dead chat:** if the newest session log was never closed (no "Left open" section, a step still in progress, or newer than the handoff), it knows the last chat ended abruptly and repairs the handoff from that log before going on.
- **During the session:** logs each step as it happens, in the user's local time; adds lasting decisions to the standing rules immediately; records links and commit hashes.
- **Checkpoint:** after each finished piece of work, updates the status table and the next-chat opening message right away, so even a chat that dies without warning leaves a correct handoff.
- **End:** closes the log, updates the handoff and history, and gives you one sentence to paste into the next chat.

**Files**
- **Draft → approve → saved:** anything public is shown to you before it's applied, then renamed `-saved` so the folder always shows what's live.
- **Nothing is deleted:** a `-previous` copy is made before any file is replaced; retired files get a `_to_delete_` prefix and you empty them yourself.
- **Stable numbering:** folder numbers never shift, even before some folders exist, so old references stay valid.

**Size and tokens**
- **English archive, your language in chat:** archive files are written in English, which costs roughly half the tokens of Persian or Arabic script. Claude still talks to you in your language. Deliverables stay in their own language, and so do your opening message (quoted verbatim) and the next-chat opening line (you paste it).
- **Heavy-chat warning:** watches for signs the chat is getting long (long back-and-forth, big files, many tool calls) and tells you once, suggesting a clean stop. It warns again only if things get clearly worse.
- **Lean handoff:** finished work is one line with a pointer; the handoff stays under about 1,500 words, so starting each chat stays cheap.

**Accumulated knowledge**
- **Technical notes:** every gotcha that cost time is written down with its fix — the thing that saves later sessions the most time.
- **Standing rules:** your decisions are stored with their reasons, so no new chat re-asks them or quietly breaks them.

**New project:** asks only what it can't infer (what the project is, where the folder lives) and builds the folder from what the chat already contains: decisions become standing rules, finished pieces go to output folders, the first plan goes to `03-plans/`.

**Environments**
- **Claude app:** a folder on your computer, connected to the chat.
- **Claude Code:** a `project-archive/` folder in the repo, plus one line in `CLAUDE.md` so every session finds it. In a private repo the archive is committed with the work; in a public repo Claude asks first.

**Safety:** never stores passwords, API keys or tokens; asks before saving anything you marked confidential; the archive is private unless you choose to publish it.

## Limits

- It's instructions and templates, no scripts: results depend on Claude following them.
- Claude has no exact meter of how full a chat is, so the heavy-chat warning is a judgement call; that's why checkpoints matter.
- One project folder per chat.
- Two chats working on the same folder at the same time aren't coordinated; re-read a file right before writing to it.

## Install

**Claude app (claude.ai, desktop, mobile)**
1. Download this repository and zip the `project-archive` folder (the folder itself, not its contents).
2. In Claude, go to **Customize › Skills**, click **+**, choose **Create skill → Upload a skill**, and upload the ZIP. Uploading again with the same name offers **Upload and replace**; earlier versions stay in the skill's history.
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
