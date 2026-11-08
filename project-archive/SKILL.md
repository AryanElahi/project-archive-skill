---
name: project-archive
description: Keep a long-running project in one folder so every new chat can pick up exactly where the last one stopped — a start-here handoff file, standing rules, a status table, one log per session, plans, and saved drafts. Use this whenever the user wants work to continue across several chats or sessions, asks to "save everything in a folder", to "archive this conversation", to make a handoff / "start here" file, says the chat is getting long or heavy, or opens a chat with "connect the folder and read the start-here file" — even if they never say "archive". Works in the Claude app (connected folder) and in Claude Code (a folder in the project); talks to the user in their language while keeping the archive in English.
---

# Project Archive

A project that spans many chats loses things between them: decisions the user already made, drafts that were approved, rules ("never mention X publicly"), the exact next step. This skill keeps all of that in one folder with a fixed shape, so the next chat reads two files and continues as if it had been there.

The folder is the memory. The chat is disposable.

## The folder

```
<project folder>/
├── 00-start-here.md      handoff: owner, source of truth, standing rules, technical notes,
│                         status table, what's left, next step, suggested opening message
├── README.md             folder map + session history table
├── 01-<source-of-truth>/ the one document everything else derives from (optional)
├── 02-sources/           snapshots of what was read before changing it ("before" state)
├── 03-plans/             one plan per upcoming piece of work
├── 04-sessions/          one log per session: YYYY-MM-DD-session-N-topic.md
└── 05-…, 06-…            output folders, created only when there is output for them
```

Keep the numbers fixed even while some folders don't exist yet (a new project may start with only `03-` and `04-`), so the order never shifts and old references stay valid. When a folder is added, add it to the layout list in `README.md`.

Templates are in `templates/` (`00-start-here.md`, `README.md`, `session-log.md`, `plan.md`); read the matching one when creating a file. A real, anonymized example of a mature folder is in `references/example.md` — read it when bootstrapping, so the result looks lived-in rather than generic.

## Language: English archive, user's language in chat

Write the archive in English — folder names, file names, the handoff, README, session logs and plans — whatever language the user speaks. Every new chat pays to read these files, and English takes roughly half the tokens of Persian or Arabic script (and fewer than most other non-Latin scripts), so an English archive keeps each session start cheap. Talk to the user in their own language as usual; only the stored bookkeeping is English.

Keep the user's language where exact wording matters or where the text is for the user rather than for the next chat:
- **Deliverables** stay in the language they're for: a Persian post is saved in Persian.
- **The user's opening message** in each session log, quoted verbatim.
- **The suggested opening message** for the next chat, since the user pastes it.
- **A short quote** of a decision when the exact words carry meaning (a name, a phrase that must or must not appear publicly); otherwise translate the decision into English.

If the user explicitly asks for the archive in their own language, do that instead. An existing folder already written in another language stays as it is; don't convert it unless the user asks.

## Where the folder lives

- **Claude app:** a folder on the user's computer, connected to the chat (ask for it by path; if the user can't connect one, keep it in your workspace and hand the files back at session end). Edit files in place with whatever file tools the session gives you; never rebuild a file by retyping it from an earlier tool result, which may be truncated.
- **Claude Code:** a folder inside the project, `project-archive/` by default. Add one line to the project's `CLAUDE.md` — "Before starting, read `project-archive/00-start-here.md`." — so every new session finds it without being told. In a private repo, commit archive changes along with the work they describe. In a public repo, ask first: logs record private decisions, and the user may prefer to add the folder to `.gitignore`.

## Session start

1. **Find the folder.** If the user names it, connect it. If none exists, bootstrap (below).
2. **Read `00-start-here.md`, then `README.md`.** Don't read everything else up front; the handoff points at what matters.
   Then glance at the newest session log. If it was never closed (no "Left open" section, steps still "in progress", or it's newer than the handoff's "last updated" line), the last chat ended abruptly: bring `00-start-here.md` and the README history up to date from that log first, and tell the user in one line that you did.
3. **Adopt the standing rules for the whole session.** They are decisions the user already made, often with reasons that are no longer in view (a product name that isn't registered yet, a number the user chose to keep). Re-asking or breaking them is the main way this system fails.
4. **Open a session log** in `04-sessions/` from the template, with the user's opening message recorded word for word.
5. **Say in one or two lines** where things stand and what you're about to do, then do it. The handoff already said what's next; don't ask permission to start it unless the plan lists open questions — then ask those, all at once.

## During the session

Log as you go, not at the end; a session can stop at any moment.

- **Steps:** one row per meaningful step in the session log's `time | step | status` table, using the user's local time. Status words: done, waiting on user, approved, blocked.
- **User decisions:** record each one close to verbatim under "Decisions", with its date if it overrides an older rule. If it should outlive the session, also add it to the standing rules in `00-start-here.md` right away.
- **Outputs:** save every draft, text, image or file into its folder. Link external things (URLs, commit hashes, artifact links) in the log.
- **Draft → approve → saved.** Show the user anything that will be published or sent before applying it. After it's applied, rename the draft to `<name>-saved.md`, so the folder always shows what is live.
- **Never delete.** Before replacing a file, copy it to `<name>-previous.md`. Retire files by renaming with a `_to_delete_` prefix; the user empties them.
- **Checkpoint after each finished piece.** When a piece of work is done (a section published, a file delivered, a decision settled), update its status row and the suggested opening message in `00-start-here.md` right then, not only at session end. If the chat dies without warning, the next one still finds a correct handoff and a ready opening line.
- **Gotchas** that cost time ("typing into this site's text boxes drops letters; insert text and read it back") go into the technical notes in `00-start-here.md`. These save the next session the most time.

## Watch how heavy the chat is

You can't see an exact meter of how full the conversation is, so watch the signs: a long back-and-forth, large files or pages read in full, many tool calls, long outputs repeated. When they pile up, say so in one line and suggest closing after the current piece — for example "This chat is getting heavy; I suggest we close the session after this and continue in a new one." The user decides. If they keep going, checkpoint more often. Say it once, and again only if it gets clearly worse; a warning that repeats every turn is noise.

## Keep the handoff lean

Every new chat pays to read `00-start-here.md`, so it holds the current state, not the history. A finished part gets one line in the status table pointing to where its detail lives (a session log or a `-saved` file); full text is for what's still open, the standing rules and the technical notes. When something finished has grown past a few lines, move the detail into the session log or the part's own file and leave the pointer. Aim for a file that reads in a minute or two — roughly 1,500 words at most.

## Session end

Do this when the work stops, when the user says the chat is getting long or heavy, or when you notice the context filling up — before it's too late to write it.

1. Close the session log: final statuses, anything left half-done and why.
2. Update `00-start-here.md`: status table, what's left, the next step (marked clearly), new rules and notes, the "last updated" line.
3. Add a row to the history table in `README.md`.
4. Write the **suggested opening message** for the next chat at the bottom of `00-start-here.md` — one sentence the user can paste, naming the folder, the two files, and the next step. In the Claude app it starts with connecting the folder; in Claude Code the folder is already there, so it just says to read the handoff.
5. Tell the user in two or three lines what was done and give them that opening message.

## Bootstrapping a new project

Ask only what you can't infer, in one go: what the project is and its goal, and where the folder should live. Then create `00-start-here.md`, `README.md`, `03-plans/` and `04-sessions/` with the first session log; create the other folders only when there's something to put in them. Fill the handoff with what the conversation already contains — owner, goal, any rules the user stated — rather than leaving placeholders. If there's no source of truth yet, say so and name what will become one once it's approved; if there are no technical notes yet, write "none yet" rather than deleting the section. If the project already has material scattered in a chat, capture it: decisions to standing rules, finished pieces to output folders, the first plan to `03-plans/`.

## What not to store

- Passwords, API keys, tokens, recovery codes — never, even if pasted in chat. Note "user has the key" instead.
- Other people's private data beyond what the project needs; ask before saving anything the user marked confidential.
- The archive is private by default. Publishing any of it (a public repo, a shared link) is the user's call.
