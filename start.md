# /start

Runs at the start of a project or session. Writes/refreshes `agents.md`.

## Steps
1. Print `[unyak debug] /start: begin`.
2. Increment `start_count` in `unyak/state.json`. Print `[unyak debug] /start: start_count=<n>`.
3. **Intent interview.** Use the questions in `templates/project-intent.md`. Ask ONE at a time, plain language. Print `[unyak debug] /start: asking intent question <n>/4` before each.
4. **Stack detection.** Scan the project for obvious stack signals: `package.json` deps, `supabase/`, `convex/`, `.env` keys (names only, never values), framework configs. Print `[unyak debug] /start: detected stack = <list or none>`.
5. **Write agents.md.** Load `templates/agents-md.md`. Fill **Project Intent** from the interview + detected stack. Write to `unyak/agents.md` (and to the project root `agents.md` if the harness expects it there). Print `[unyak debug] /start: wrote agents.md (intent + stack)`.
6. Do NOT overwrite an existing populated Project Intent block silently — confirm with the user first. Print `[unyak debug] /start: existing intent found, confirming overwrite` if applicable.
7. Trigger the "after /start" promotion (router handles one-per-session rule).

## Output to user
Short confirmation: what was captured, detected stack, and that agents.md is ready. Then the promotion line.
