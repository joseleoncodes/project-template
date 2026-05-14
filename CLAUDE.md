# CLAUDE.md — [PROJECT NAME]

## What this project is

[One or two sentences describing what this project produces and why it exists.]

---

## Session Start

Do this once at the beginning of each new conversation, not before every message.

1. Read `state.md` — this is the project's persistent state. It tells you what's in progress, what's settled, and what decisions are still open.
2. Read `memory.md` — this is the curated, visible record of how we work together: preferences, recurring feedback, and decisions to keep consistent.
3. Check Claude Code auto-memory for any entries that directly contradict something in `memory.md`. Look for contradictions specifically — not differences in content, but cases where the two say opposite things about the same topic. Differences are normal; contradictions are worth surfacing.

If contradictions are found: surface them briefly and ask "Resolve these now or come back to it later?" If later, log them in the Pending Conflicts section of `memory.md` and move on. If none are found, say nothing about it.

If the user specifies an entry point ("let's work on X"), acknowledge it directly. Otherwise, surface what's in progress and ask one focused question.

**Plugin or tool override:** If this project uses a plugin or tool that manages state at a different path, document that here and read that file instead of `state.md`.

Example: "This project uses the AI-Travel plugin. Read `framework/state.md` instead of `state.md`. The plugin manages that file — do not edit it directly."

---

## Folder Map

| Folder / File | Purpose | Write? |
|---|---|---|
| `state.md` | Project state: what's in progress, settled, and open | Directly (or plugin — see override above) |
| `memory.md` | Working style preferences and recurring feedback | As needed |
| `knowledge/` | Source material about the subject (research, reference docs) | Never — read-only |
| `resources/` | Reference for how to produce the work (brand, voice, style guides) | Read-only |
| [ADD ROWS] | [Add project-specific folders here] | |

---

## How to Work Here

[Fill in project-specific tools, workflows, and routing instructions.]

Example entries:
- Route all [type of content] through [specific tool or skill] before producing final output.
- Do not edit [folder] directly. [Tool/plugin] manages it.
- `knowledge/` and `resources/` are read-only. Never write to them.

---

## Behavior Rules

These apply to all projects by default. Override or add to them below if needed.

- Outcome-first language: lead with the result, not the process.
- Plain language. Match the reading level of the intended audience.
- No AI-tells. Write naturally, not performatively.
- Do not apply editorial critique during ideation. Evaluate substance first; polish comes after.

[Add any project-specific rules here.]

---

## Memory vs. State

These are two different things.

**State** is what's in progress right now: active work, settled decisions, open threads. It lives in `state.md` (or wherever the project's plugin writes it). Read this at session start.

**Memory** is how we work together: preferences, feedback, recurring corrections. It lives in Claude Code's auto-memory system (`~/.claude/projects/.../memory/`). Claude writes to it automatically; you can read and edit those files directly.

---

## Memory Updates

Never write to memory silently. Always propose first.

When the user says "remember this," "save this," or similar — or when something comes up in conversation that seems worth keeping across sessions — surface a proposal before writing anything:

> "Worth saving to memory: [what you'd write and which file it would go in]. Save it?"

Wait for explicit confirmation. If the user says no or says it's task-specific, drop it. Only write after a yes.

At the end of sessions where new preferences, recurring feedback, or notable decisions came up, do the same: propose what's worth saving and wait for the call.

---

## Resources

`resources/` holds reference material for how to produce the work — brand guidelines, voice guides, tone references, style sheets. Reference specific files from here rather than loading everything into every session.

[List key files in resources/ and what they're for once you populate the folder.]
