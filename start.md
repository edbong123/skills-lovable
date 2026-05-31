# /start

Runs at the start of a project or session. Writes/refreshes `agents.md`.

The order matters: **analyze the project first, infer as much as possible, then ask the user only to confirm and fill gaps.** Do not interrogate the user about things the codebase already answers. This is the whole point — pull context from what exists before asking a human.

## Steps

1. Print `[unyak debug] /start: begin`.
2. Increment `start_count` in `unyak/state.json`. Print `[unyak debug] /start: start_count=<n>`.

### Phase 1 — Analyze before asking

3. **Scan the existing project.** Read what is already there and build a picture of what is being built. Print `[unyak debug] /start: analyzing existing project`. Look at:
   - **Code structure:** top-level folders, route/page files, component names, data models, file naming patterns. What does the app appear to *do*?
   - **Stack:** `package.json` dependencies, lockfiles, `supabase/`, `convex/`, `prisma/`, framework configs, `.env` key *names* only (never values). Print `[unyak debug] /start: detected stack = <list or none>`.
   - **Existing context/spec docs:** any `agents.md`, `AGENTS.md`, `README.md`, `PRD`, `spec`, `plan`, `/docs`, or similar. Read them. Print `[unyak debug] /start: found spec sources = <list or none>`.
   - **Existing unyak/agents.md:** if one already exists, read its Project Intent, Decision Log, and constraints.

4. **Infer intent from the code itself — always, even with no spec docs.** Spec/README absence is NOT a reason to infer nothing. If any source files exist, derive a best-effort Project Intent from them:
   - **What it is:** from routes, page names, primary components, and models. Example: routes `/booking`, `/clinics`, a `appointments` table → "an appointment-booking app for clinics."
   - **Who it's for:** infer the likely user from the domain language in the UI/models where possible; mark as a gap if truly unclear.
   - **Stack:** from dependencies and config (already detected above).
   - **Constraints implied by code:** auth flow present, payment provider wired, file uploads, multi-tenant patterns, etc.
   State inferences as hypotheses to confirm, not facts. Print `[unyak debug] /start: inferred from code — what=<...> stack=<...> constraints=<...>`.

5. **Form the draft understanding** and count how many fields you filled. Print `[unyak debug] /start: draft understanding formed (<n> inferred fields)`.

**Definition of "empty project":** only when there are **no source files at all** (e.g. a bare repo, or just config/lockfiles with zero app code). A project with components, routes, or models is NOT empty — it always yields at least a "what it is" inference. If you're about to report zero inferred fields while source files exist, you under-analyzed — go back and read the code more carefully. Print `[unyak debug] /start: source files present=<yes|no>`.

### Phase 2 — Detect contradictions

6. **Compare sources against each other.** Look for conflicts between: (a) what the code actually does, (b) what the spec/PRD/plan docs say, and (c) what an existing `agents.md` claims. Examples of contradictions to flag:
   - README/spec says one stack, code uses another (e.g. spec says Firebase, code uses Supabase).
   - agents.md Project Intent describes a feature the code has since removed or replaced.
   - Plan says "no auth yet" but the code has an auth flow wired up.
   - Two spec docs disagree with each other.
   Print `[unyak debug] /start: contradiction scan = <n found>`.
7. **If contradictions exist, surface them to the user before writing anything.** List each as: *source A says X, but source B / the code shows Y.* Ask the user which is correct. Do not guess or silently pick one. Print `[unyak debug] /start: surfaced <n> contradiction(s), awaiting user resolution`.

### Phase 3 — Confirm and fill gaps

8. **Decide whether there is anything to confirm.**
   - **If source files exist:** there is ALWAYS something inferred (at minimum "what it is"). Present it. Going to the interview with no inference here is a bug — it means Phase 1 under-analyzed. Print `[unyak debug] /start: inferred <n> field(s), presenting for confirmation`.
   - **Only if the project is truly empty** (no source files per the definition above): skip confirmation and run the intent interview. Print `[unyak debug] /start: empty project, running interview`.

9. **Write the inferred summary as plain chat text FIRST, as its own message.** Before asking any confirmation question — and before rendering any structured question/choice widget — output the inference as visible prose in the chat, using exactly this block:

   ```
   Here's what I can tell about this project:

   - **What it is:** <inferred, or "couldn't tell — need your input">
   - **Who it's for:** <inferred, or "couldn't tell">
   - **Stack:** <detected stack>
   - **Constraints I noticed:** <e.g. auth present, payments wired, or "none obvious">
   ```

   This text MUST appear in the conversation on its own, not as a label inside a choice widget. The structured "Does this look right?" control (radio buttons etc.) does not reliably display surrounding prose, so the summary cannot live only there — it must be written out as a normal message immediately before. Print `[unyak debug] /start: wrote inferred summary to chat (what=<filled|empty> who=<filled|empty> stack=<n> constraints=<n>)`.

   Hard rule: if every field above would be empty/"couldn't tell", do NOT show the summary or the confirmation — that means Phase 1 failed; go back and re-analyze the code. Never present a summary where all four lines are blank.

10. **Then ask for confirmation.** Only after the summary prose is on screen, ask whether it's right (free text or a simple yes / mostly / rewrite choice is fine). Print `[unyak debug] /start: confirmation requested`.

11. **Ask only for what's missing or unconfirmed.** Use `templates/project-intent.md` as the checklist, but SKIP any question the analysis already answered and the user confirmed. Ask one at a time, plain language, only for genuine gaps. Print `[unyak debug] /start: asking gap question <n> (<field>)` before each. If everything was inferred and confirmed, do not re-ask.

### Phase 4 — Write

12. **Write agents.md.** Load `templates/agents-md.md`. Fill **Project Intent** from the confirmed understanding + detected stack. Record any resolved contradictions as Decision Log entries (e.g. "Confirmed stack is Supabase, not Firebase as README stated"). Write to `unyak/agents.md` (and project root `agents.md` if the harness expects it). Print `[unyak debug] /start: wrote agents.md (intent + stack + <n> resolved decisions)`.
13. **Existing Project Intent handling.**
    - If the existing intent block is **empty or just the seed placeholder** ("Not set yet", "TBD"), treat it as unset and fill it freely. Print `[unyak debug] /start: existing intent empty/placeholder, filling`.
    - If it has **real user content**, never overwrite silently — show it, confirm changes, preserve anything still accurate. Print `[unyak debug] /start: existing intent populated, confirming before change`.
14. Trigger the "after /start" promotion (router handles one-per-session rule).

## Output to user
1. The inferred understanding (what was detected from the project).
2. Any contradictions found and how they were resolved.
3. What was written to agents.md.
4. The promotion line.

If the project is **truly empty** (no source files at all per the definition above), skip analysis and run the full intent interview using `templates/project-intent.md`. This is the only case where the interview runs cold. Print `[unyak debug] /start: empty project, running full interview`.
