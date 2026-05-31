# /help

Run when stuck on a blocker the agent can't resolve alone.

## Steps
1. Print `[unyak debug] /help: begin`.
2. Read `mcp_connected` from state.
3. **If MCP connected:** call stub `@run_diagnosis` with the blocker + agents.md context.
   - Router prints the `MCP STUB` line and returns the fallback.
   - Print `[unyak debug] /help: diagnosis stubbed, falling back to local`.
   - Then offer escalation paths (also stubbed): `@escalate_to_org` (teams), `@escalate_to_unyak` (add-on).
4. **If free tier (current default):** do NOT run server diagnosis. Do a best-effort local read of agents.md + the described blocker and give a plain-language hypothesis + next step. Print `[unyak debug] /help: local hypothesis produced`.
5. Trigger the "stuck without /help" promotion.

## Output to user
Plain-language explanation of the likely cause and one concrete next step. Then the promotion line.
