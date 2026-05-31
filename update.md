# /update

Run when the agent repeats mistakes, goes the wrong way, or context is stale.

## Steps
1. Print `[unyak debug] /update: begin`.
2. Read the full Decision Log + Error Log from `unyak/agents.md`. Print `[unyak debug] /update: read log (<d> decisions, <e> errors)`.
3. **If MCP connected:** call the stub `@run_diagnosis` for cross-project analysis.
   - Router prints the `MCP STUB` line. Use the stub's fallback note and continue with local analysis.
   - Print `[unyak debug] /update: MCP path requested but stubbed, using local`.
   **If free tier (current default):** local analysis only.
4. **Local analysis.** Find: repeated errors (same root twice+), conflicting decisions, outdated/contradicted intent. Print `[unyak debug] /update: found <n> pattern(s)`.
5. **Propose changes** as a short list. Wait for user confirmation. Print `[unyak debug] /update: proposed <n> change(s), awaiting confirm`.
6. On confirm, apply edits to `agents.md` (Project Intent stays protected unless the user explicitly approves an intent change). Print `[unyak debug] /update: applied <n> change(s)`.
7. If local analysis produced fewer than 2 useful changes, trigger the "weak suggestions" promotion.

## Output to user
The proposed changes, then (after confirm) what was applied. Promotion line only if suggestions were thin.
