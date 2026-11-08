# Example: a personal-brand project after four sessions

A real archive, anonymized. The project: rebuild a person's professional profiles (resume, a professional network profile, code-hosting profile, personal site) and then start writing posts. Four chats in, the folder looks like this:

```
resume/
├── 00-start-here.md
├── README.md
├── 01-master-resume/
│   └── Master-Resume.md                 approved source of truth (+ link to a live editable copy)
├── 02-sources/
│   ├── site-before.md                   what the personal site said before any change
│   ├── profile-before.md
│   └── repos-before.md
├── 03-plans/
│   ├── profile-plan.md                  section by section, with content ideas for later posts
│   ├── site-plan.md                     next big piece of work
│   └── archive-skill-plan.md
├── 04-sessions/
│   ├── 2026-11-04-session-1-master-resume.md
│   ├── 2026-11-04-session-2-profile.md
│   ├── 2026-11-06-session-3-profile.md
│   └── 2026-11-08-session-4-repos.md
├── 05-profile/
│   ├── About-saved.md                   live text, renamed from the draft after it was applied
│   ├── About-previous.md                backup taken before replacing
│   ├── Experience-saved.md
│   └── …
├── 06-images/
│   ├── banner-v1.png  +  banner source HTML
│   ├── images-needed.md                 what the user still has to send
│   └── from-user/
└── 07-repos/
    ├── profile-README.md
    └── profile-README-previous.md
```

## What made it work

**Standing rules carried real weight.** Examples of the kind of rule that belongs there:
- Two product names must never appear publicly (not registered yet) — only a generic description.
- One project's headline number stays as the user chose on the public profile, while the repo states the figure the notebooks actually support; a third, wrong figure that circulated earlier is banned everywhere.
- A few minor old items appear only in the master document, never in public versions.
- Show any profile text to the user before saving it.

Without these, a fresh chat would "helpfully" reintroduce a banned name or "correct" a number the user had deliberately decided on.

**The status table was the dashboard.** One row per profile section — headline, about, experience, education, projects, banner, photo, honors, certifications, skills, featured, languages, publications — each "done" with a pointer to its `-saved.md` file, or "waiting on user — <what>". Rows the user cancelled stayed, marked "cancelled — user's decision", so nobody redid them.

**Technical notes saved hours.** For instance: one site rewrites the headline automatically whenever a role is added, so restore it afterwards; typing into its text boxes drops characters, so insert the text and read it back; file uploads only accept files from Claude's own working directory; the built-in browser isn't signed in to that site, so use the user's Chrome.

**Session logs recorded decisions with times.** A typical row: `07:13 | User: repo name is Claude's choice; co-author agreed to publish; add the organizer's name; include model outputs; leave out the slide decks; keep the public profile number unchanged | approved`. When the next session wondered why model outputs were in the repo, the answer was one search away.

**The opening message closed the loop.** The bottom of `00-start-here.md` held a sentence for each upcoming session, ready to paste: connect the folder, read the two files, continue with the named step.

**Nothing was deleted.** Temporary archives went into `_to_delete/`; the user cleared them. A side folder the user wanted removed was listed in "Still to do" as "user will delete".
