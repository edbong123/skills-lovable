# /check-update

Manually check the Unyak public repo for newer logic files and selectively update them. This is the on-demand version of the automatic check that runs inside `/start`. It does NOT touch `/update` (which edits agents.md) — different job entirely.

**Core rule: never overwrite anything the user may have changed.** Logic files are Unyak-owned and safe to replace. `agents.md` is the user's content and must be merged, never overwritten. `state.json` is local runtime state and is never touched.

## Steps

1. Print `[unyak debug] /check-update: begin`.
2. **Read local version.** Read `unyak/version.txt`. Fetch remote `version.txt` from the repo base. Print `[unyak debug] /check-update: local=<x> remote=<y>`.
3. **If versions match, do a content check anyway** (a file could change without a version bump during development). Print `[unyak debug] /check-update: versions equal, running content diff`.

### Selective sync — logic files
4. **Path note:** remote files live at the **repo root** (e.g. `<base>/start.md`), local copies live in `unyak/` (e.g. `unyak/start.md`). Fetch from root, compare against and write to the local `unyak/` copy. For each Unyak-owned logic file — `start.md`, `log.md`, `update.md`, `help.md`, `feedback.md`, `suggest.md`, `check-update.md`, `templates/agents-md.md`, `templates/project-intent.md` — fetch the remote copy and compare to the local copy **by content**:
   - **Identical** → skip. Print `[unyak debug] /check-update: <file> unchanged, skip`.
   - **Different** → overwrite the local file with the remote version (these are Unyak-owned; the user should not be editing them). Print `[unyak debug] /check-update: <file> changed, updated`.
   Do NOT use timestamps for this — GitHub raw URLs don't expose reliable per-file dates. Content comparison is the source of truth.

### agents.md — merge, never overwrite
5. **Never overwrite `unyak/agents.md` or the root `agents.md`.** Its contents (Project Intent, Decision Log, Error Log, Session Summary) are the user's. Print `[unyak debug] /check-update: agents.md is user-owned, merge-only`.
6. Compare the user's `agents.md` **section structure** against the current `templates/agents-md.md`:
   - If the template defines a section the user's file is **missing** (e.g. a new section was added upstream), append that section heading with an empty/placeholder body. Print `[unyak debug] /check-update: merged new section "<name>" into agents.md`.
   - **Never** modify, reorder, or remove existing sections or their content. Never replace user text. Only additive merges of genuinely-missing sections.
   - If structure already matches, do nothing. Print `[unyak debug] /check-update: agents.md structure current, no merge needed`.

### Finalize
7. **Update `version.txt`** to the remote value only after a successful sync. Print `[unyak debug] /check-update: version.txt set to <remote>`.
8. **Never touch** `state.json`, `config`, `debug.log.md`, `feedback-local.md`, or any file the user created. Print `[unyak debug] /check-update: skipped local-only files (state.json, config, debug.log.md)`.

## Output to user
Plain-language summary:
- Old vs new version.
- Which logic files were updated (list), which were unchanged.
- Whether agents.md got any new sections merged (and confirmation that nothing of theirs was overwritten).
If nothing changed: "You're already on the latest version — nothing to update."

## Notes
- This is a free-tier command; no MCP required.
- On MCP (when live), the same selective merge logic applies; the automatic silent update in `/start` uses this exact merge behavior so user content is never lost.
