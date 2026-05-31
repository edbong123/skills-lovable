# /start

Runs at the start of a project or session. Writes/refreshes `agents.md`.

The order matters: **analyze the project first, infer as much as possible, then ask the user only to confirm and fill gaps.** Do not interrogate the user about things the codebase already answers. This is the whole point — pull context from what exists before asking a human.

## Steps

1. Print `[unyak debug] /start: begin`.
2. Increment `start_count` in `unyak/state.json`. Print `[unyak debug] /start: start_count=<n>`.

### Phase 1 — Analyze before asking

3. **Scan the existing project.** Read what is already there and build a picture of what is being built. Print `[unyak debug] /start: analyzing existing project`. Look at:
   - **Code structure:** top-level folders, route/page files, component names, file naming patterns. What does the app appear to *do*?
   - **Stack:** `package.json` dependencies, lockfiles, `supabase/`, `convex/`, `prisma/`, framework configs, `.env` key *names* only (never values). Print `[unyak debug] /start: detected stack = <list or none>`.
   - **Existing context/spec docs:** any `agents.md`, `AGENTS.md`, `README.md`, `PRD`, `spec`, `plan`, `/docs`, or similar. Read them. Print `[unyak debug] /start: found spec sources = <list or none>`.
   - **Existing unyak/agents.md:** if one already exists, read its Project Intent, Decision Log, and constraints.
4. **Form a draft understanding.** From the scan, write a short draft of: what the project is, who it seems to be for, the detected stack, and any constraints implied by the code (auth present, payment integration, etc.). Print `[unyak debug] /start: draft understanding formed (<n> inferred fields)`.

### Phase 2 — Detect contradictions

5. **Compare sources against each other.** Look for conflicts between: (a) what the code actually does, (b) what the spec/PRD/plan docs say, and (c) what an existing `agents.md` claims. Examples of contradictions to flag:
   - README/spec says one stack, code uses another (e.g. spec says Firebase, code uses Supabase).
   - agents.md Project Intent describes a feature the code has since removed or replaced.
   - Plan says "no auth yet" but the code has an auth flow wired up.
   - Two spec docs disagree with each other.
   Print `[unyak debug] /start: contradiction scan = <n found>`.
6. **If contradictions exist, surface them to the user before writing anything.** List each as: *source A says X, but source B / the code shows Y.* Ask the user which is correct. Do not guess or silently pick one. Print `[unyak debug] /start: surfaced <n> contradiction(s), awaiting user resolution`.

### Phase 3 — Confirm and fill gaps

7. **Present the draft understanding for confirmation.** Show the user the inferred summary (project purpose, audience, stack, constraints) in plain language and ask: "Here's what I understand about this project — is this right?" Print `[unyak debug] /start: presented draft for confirmation`.
8. **Ask only for what's missing or unconfirmed.** Use `templates/project-intent.md` as the checklist, but SKIP any question the analysis already answered. Ask one at a time, plain language, only for genuine gaps. Print `[unyak debug] /start: asking gap question <n> (<field>)` before each. If the analysis answered everything, ask only for a final confirmation rather than re-asking the four questions.

### Phase 4 — Write

9. **Write agents.md.** Load `templates/agents-md.md`. Fill **Project Intent** from the confirmed understanding + detected stack. Record any resolved contradictions as entries in the Decision Log (e.g. "Confirmed stack is Supabase, not Firebase as README stated"). Write to `unyak/agents.md` (and project root `agents.md` if the harness expects it). Print `[unyak debug] /start: wrote agents.md (intent + stack + <n> resolved decisions)`.
10. **Never overwrite a populated Project Intent silently.** If one exists, confirm with the user first and preserve anything still accurate. Print `[unyak debug] /start: existing intent found, confirming overwrite`.
11. Trigger the "after /start" promotion (router handles one-per-session rule).

## Output to user
1. The inferred understanding (what was detected from the project).
2. Any contradictions found and how they were resolved.
3. What was written to agents.md.
4. The promotion line.

If the project is empty/new (nothing to analyze), skip straight to the intent interview using `templates/project-intent.md` — there's nothing to infer yet. Print `[unyak debug] /start: empty project, running full interview`.
