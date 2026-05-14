Claude uses a small set of files and “scopes” to manage behavior, memory, and state across CoWork and Claude Code.

Below is how `CLAUDE.md`, `MEMORY.md`, and related files work, what levels they operate at (user / project / directory / local / managed), and how to think about “memory vs state” in the context of Claude Code.

---

## Big picture: levels and scopes

Claude settings and context files are layered by scope.

Typical scopes:

- Managed (org / machine level; deployed by IT, MDM, etc.)
- User (your home directory; applies to all projects)
- Project / repo (inside a given repository; shared via git)
- Local-in-project (your personal overrides for that one repo; usually gitignored)
- Directory / subdirectory (more specific rules as you go deeper into the tree)

Claude resolves conflicts by giving more specific scopes higher priority (e.g., directory > project > user > managed, depending on the setting).

---

## `CLAUDE.md`: what it is

`CLAUDE.md` is a markdown “rules / instructions” file that Claude automatically reads and injects into its system prompt when working in that scope.

It is for “what you want Claude to do and how you want it to behave,” not for automatically learned facts about the project.

Typical contents: coding standards, project architecture, workflows, naming conventions, preferred tools, and instructions for how to collaborate.

---

## `CLAUDE.md` levels and what’s visible / controllable

### User-level `CLAUDE.md`

- Location: commonly `~/.claude/CLAUDE.md` on your machine.
- Scope: affects you across all projects on that machine, but is not shared with anyone else.
- Visibility:
  - Hidden from other users; not committed to git.
  - Fully under your control; you edit it directly.
- Use-cases: your personal preferences and stable habits.
  - How you like code formatted when there’s no project standard.
  - How you want Claude to address you, general collaboration style.
  - Default tools / workflows you prefer (e.g., “prefer pytest,” “prefer pnpm”).

Conceptually: “Your default operating system for Claude,” which can be overridden by more specific project / directory rules.

---

### Project-level `CLAUDE.md`

- Locations:
  - Commonly `./CLAUDE.md` at repo root, or `./.claude/CLAUDE.md`.
- Scope: all users working in that repository (CoWork and Claude Code) receive those rules when they work in the repo.
- Visibility:
  - Visible and editable to everyone with repo access.
  - Committed to git, so it’s shared across machines and team members.
- Use-cases: team / project standards and onboarding.
  - Coding conventions, folder structure, architecture overview.
  - How to build, test, deploy.
  - Project-specific workflows, e.g. “use `make test` before opening PRs.”

In conflicts, project-level `CLAUDE.md` typically overrides user-level instructions because it’s more specific to the work you’re doing.

---

### Directory / subdirectory `CLAUDE.md` (package / module level)

- Location: any subdirectory, e.g. `packages/api/CLAUDE.md` or `services/billing/CLAUDE.md`.
- Scope: only applies when Claude is working in that directory subtree (reading / editing files there).
- Loading behavior:
  - Not loaded at session start.
  - Loaded “on demand” as Claude traverses into that part of the tree.
- Use-cases: module-specific rules.
  - Microservice-specific conventions, domain vocab, data models.
  - Stack-specific patterns (e.g., “in the `mobile/` directory, use React Native patterns X,Y,Z”).

More specific directories override broader project-level rules where they conflict, e.g., API service uses one logging library while the monolith uses another.

---

### Organization / “team-level” usage

Some setups also describe a “team-level” layer where a shared `CLAUDE.md` (or similar centralized config) is referenced across multiple repos, often via a bootstrap repo or workspace.

This is more of a pattern than a built-in special file type: a team may maintain a canonical `CLAUDE.md` and synchronize or include it in multiple repos to enforce consistent conventions.

---

## `MEMORY.md` and Claude’s memory system

Claude’s project memory is implemented via a separate `MEMORY.md` entrypoint (and optional topic files), not through `CLAUDE.md`.

### How `MEMORY.md` works

- Location: configurable directory (often per repo); the main file is `MEMORY.md` with optional topic files alongside it.
- Scope: per repository, across worktrees (i.e., repo-level memory shared for that codebase).
- Loading behavior:
  - On each conversation, Claude automatically loads the first part of `MEMORY.md` into context.
  - Additional detail is sharded into topic files which are referenced but not fully loaded up front.
- Contents: “learnings and patterns” that Claude discovers over time.
  - Build commands, debugging tricks, dependency gotchas.
  - Clarified domain concepts, recurring edge cases.
- Contrast with `CLAUDE.md`:
  - `CLAUDE.md` is for instructions and rules you author.
  - `MEMORY.md` captures what Claude learns organically while working with you.

Users can influence memory by editing `MEMORY.md` directly or by configuring where the memory directory lives, but individual auto-written entries are typically managed by Claude.

---

## Memory levels and what’s user-visible

You can think of three “memory levels” coexisting: project, user, and auto-memory.

- User memory (broad): things Claude remembers about you personally, often stored outside the repo in a user-level memory path.
- Project memory (repo-specific): `MEMORY.md` + topic files for that project.
- Auto-memory: structured updates that Claude writes as it learns.

Visibility and control:

- You can open and edit `MEMORY.md` just like any other markdown file; nothing is inherently “hidden” if you know where it’s configured.
- You can constrain where auto memory is stored via settings (e.g., an absolute path), but those settings may be restricted to certain scopes for security reasons.

---

## Settings and other files beyond `CLAUDE.md` / `MEMORY.md`

There are several other files that help manage Claude Code and CoWork behavior.

### `settings.json` and `settings.local.json`

- Typical locations:
  - User settings: `~/.claude/settings.json` (applies across all projects).
  - Project settings: `.claude/settings.json` at repo root.
  - Local overrides: `.claude/settings.local.json` (you only, in that repo; usually gitignored).
- Scope vs sharing:
  - Managed: can be deployed centrally for all users on a machine.
  - User: not shared, just your machine.
  - Project: shared via git with collaborators.
  - Local: not shared, repo-specific overrides.
- Purpose: configure permissions, tools, hooks, and integration behavior.
  - Which tools Claude can access.
  - What hooks run on save or after certain commands.
  - Where to store project memory directories.

This is more of a “state and capabilities configuration” layer than instruction text.

---

### `.claude/rules/*.md`

Additional rule files can live in `.claude/rules/` and are automatically loaded alongside the main `CLAUDE.md` with the same priority.

Examples:

- `memory-profile.md` (facts about you).
- `memory-preferences.md` (how you like things done).
- `memory-decisions.md` (past choices you want kept consistent).
- `sessions.md` (rolling summary of recent work).

This is a pattern some advanced users use to modularize “manual memory / rules” as opposed to everything living in a single `CLAUDE.md`.

---

### `skills/` and `commands/`

Inside `.claude/` you can also have:

- `.claude/skills/<skill-name>/SKILL.md`
  - These are project-level skills that Claude can load on demand when relevant, not always injected like `CLAUDE.md`.
  - Good for detailed, specialized knowledge bases (e.g., “FHIR skill,” “DICOM workflow skill”).

- `.claude/commands/*.md`
  - Slash commands: each file becomes a `/command` you can invoke.
  - Good for repeatable sequences like “run tests and fix,” “rebuild docs,” “bump version.”

Both of these are “capability extensions” rather than persistent memory per se.

---

## Is there a `state.md` or equivalent?

There is no standard `state.md` file in the common project-structure patterns; state is generally managed via:

- Settings files (`settings.json`, `settings.local.json`) for permissions, tools, hooks, and memory directory location.
- Memory files (`MEMORY.md` and siblings) for learned project knowledge.
- The ephemeral in-session conversation state, which isn’t exposed as a file but is just the current chat transcript and context window.

Some guides might informally talk about “state” in prose, but there’s no canonical `state.md` in the documented file hierarchy like there is for `CLAUDE.md` and `MEMORY.md`.

---

## Memory vs state in Claude Code

Given your intuition that “memory” and “state” are different, here’s how it maps onto Claude Code in practice:

### Memory (persistent knowledge)

- Long-lived, explicitly written or auto-written records.
- Survive restarts, editor closures, machine reboots.
- Implemented via:
  - `CLAUDE.md` and `.claude/rules/*.md`: persistent *instructions* and conventions.
  - `MEMORY.md` and topic files: persistent *learnings* and patterns.
  - User memory and settings: preferences / defaults across projects.

You can treat `CLAUDE.md` and `MEMORY.md` as the two pillars of persistent memory: “what you tell Claude to do” vs “what Claude infers and records.”

### State (runtime / session context)

- Short-lived, dynamic, and often implicit.
- Examples:
  - Current conversation history.
  - The exact files currently open or recently edited.
  - Temporary reasoning paths for a given task.
- Represented in:
  - The live chat context (not a file).
  - Some aspects of `settings.json` and environment (enabled tools, hooks, memory path) that shape how Claude can act *right now*.

From a mental model perspective:

- **Memory** = durable knowledge and instructions across sessions (CLAUDE + MEMORY + rules).
- **State** = the transient situation in this particular session plus configuration that controls behavior but isn’t itself “knowledge” in the same sense (settings, enabled tools, current selection).

---

## How much can the user influence at each level?

Putting it all together:

- Managed level (org / machine):
  - You might have limited control if IT manages it.
  - Can enforce tool permissions and default memory paths.

- User level:
  - You fully control `~/.claude/CLAUDE.md` and `~/.claude/settings.json` and any user-level memory directory.
  - Hidden from other users; great for personal preferences and default behaviors.

- Project level:
  - You and collaborators jointly control `./CLAUDE.md`, `.claude/CLAUDE.md`, `.claude/settings.json`, `.claude/rules/`, `.claude/skills/`, `.claude/commands/`, and the project `MEMORY.md`.
  - Visible to everyone in the repo; ideal for team standards and project memory.

- Local-in-project level:
  - You alone control `.claude/settings.local.json` (usually gitignored).
  - Useful for private overrides: “enable this experimental tool just for me,” “use this local memory path,” etc.

- Directory level:
  - Any collaborator can create `CLAUDE.md` files in subdirectories to define module-specific rules.
  - They override broader rules when Claude is working in that subtree.

If you want to explicitly encode “state-like” behavior (e.g., running a multi-step workflow, summarizing a workspace), you generally use commands or skills plus `CLAUDE.md` instructions, while “memory” for that workflow would live in your rules / memory files.

---

## Conversation management tools

Claude Code has three tools for undoing or branching a conversation. They appear when you hover over a message in the thread.

### Fork conversation from here

Branches the conversation at that message into a new separate thread. The original thread is untouched. Files on disk do not change.

**When to use:** The conversation went in the wrong direction, or you accidentally pasted context meant for a different project into this thread. Fork from the last good message and continue in the new branch. The original stays intact for reference.

---

### Rewind code to here

Reverts all file changes Claude made after that message back to what they were at that point. The conversation history stays visible — only the files change.

**When to use:** Claude went down a bad path and made file changes you don't want — whether that's source code in a dev project or documents and markdown files in a content project. Rewind gets the files back without manual undoing.

**Important — if you back up to GitHub:** Rewind code is most useful *before* you've committed. Once changes are committed and pushed, use git instead (`git revert` or `git checkout`) — it's more precise and won't create a divergence between your local files and what's on GitHub. If you rewind code after pushing, your local state and GitHub will be out of sync.

---

### Fork conversation and rewind code

Does both at the same time: branches the conversation into a new thread AND reverts the files to match that point.

**When to use:** Both things went wrong together — the conversation drifted in the wrong direction AND Claude made file changes you don't want. Use this when continuing from the current conversation would be confusing even if the files were fixed, and you want a clean slate on both without losing the original thread.

**Important — if you back up to GitHub:** The same caveat as Rewind code applies here. The rewind part of this tool reverts local files, but anything already committed and pushed stays on GitHub — leaving your local state and GitHub out of sync. This warning matters even more here: by the time both the conversation and the files have gone wrong badly enough to reach for this option, there's a good chance something was already committed along the way. Check your git status before using it, and if commits have been pushed, use git to handle the file revert instead.