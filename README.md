# project-template

A reusable starting point for any project that uses Claude Code or Claude Co-work. Clone it, fill in the placeholders, and you have a Level Zero Constitution ready from session one.

---

## What's included

| File / Folder | Purpose |
|---|---|
| `CLAUDE.md` | The project constitution. Claude reads this at session start. |
| `memory.md` | Working style preferences and recurring feedback for this project. |
| `state.md` | Persistent project state — what's in progress, settled, and open. |
| `resources/` | Brand guidelines, voice guides, style references for how to produce the work. |

---

## How to use for a new project

1. Clone or copy this repo into your new project directory.
2. Fill in the `[FILL IN]` and `[PROJECT NAME]` placeholders in each file.
3. Delete instructions and example entries you don't need.
4. Populate `resources/` with any brand, voice, or style reference files.
5. Open a Claude session and it will load `CLAUDE.md` automatically.

---

## How to retrofit an existing project

1. Copy `CLAUDE.md`, `memory.md`, and `state.md` into the project root.
2. Update `CLAUDE.md` with the project's actual folder structure and any existing tools or plugins.
3. Move any heavy reference files (brand guides, style docs) into a `resources/` folder.
4. Migrate any scattered project instructions or preferences into the appropriate file (`CLAUDE.md` for rules, `memory.md` for preferences, `state.md` for current work).

---

## Memory vs. State

These are intentionally two different files.

**State** (`state.md`) is volatile — it changes every session. It tracks what's in progress, what's done, and what's waiting. Think of it as the project's short-term working memory.

**Memory** (`memory.md`) is stable — it captures how you work, not what you're working on. Preferences, recurring feedback, decisions to keep consistent across sessions.

Claude Code also has an auto-memory system (`~/.claude/projects/.../memory/`) that writes cross-session learnings automatically. Use `memory.md` for things you want to state explicitly; let the auto-memory handle the rest.

---

## Plugin projects

If a plugin manages state at a custom path (e.g., `framework/state.md`), document that in the `CLAUDE.md` override note and read that file instead of `state.md` at session start. Don't fight the plugin's hardcoded paths — just tell Claude where to look.

---

## Folder naming

`resources/` is the standard name for reference material that describes how to produce the work. If your project has subject-matter reference (research, source docs, raw knowledge), keep that in a separate `knowledge/` folder. The distinction: `knowledge/` is about what you're making; `resources/` is about how to make it.
