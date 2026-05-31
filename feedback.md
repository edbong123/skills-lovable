# /feedback

Send product feedback to Unyak. All plans.

## Steps
1. Print `[unyak debug] /feedback: begin`.
2. Ask for one message (bug, feature request, impression). Print `[unyak debug] /feedback: awaiting message`.
3. **Offer to attach the debug log.** Ask the user: "Attach your recent Unyak debug log? It helps us diagnose issues. It contains only Unyak's own debug output — no code or secrets." Default to yes for bug reports. Print `[unyak debug] /feedback: debug log attach = <yes|no>`.
   - If yes, read the recent entries from `unyak/debug.log.md` (cap at the last ~200 lines to keep payload small) and include them with the feedback. Print `[unyak debug] /feedback: attached <n> debug lines`.
   - Never attach `agents.md`, project code, `.env`, or `state.json` — only `debug.log.md` content.
4. Call stub `@send_feedback` with the message + basic project metadata (project name, harness) + the debug log if attached.
   - Router prints the `MCP STUB` line.
   - Print `[unyak debug] /feedback: transport stubbed, captured locally`.
5. Append the message (and a note of whether the log was attached) to `unyak/feedback-local.md` so it isn't lost while transport is stubbed. Print `[unyak debug] /feedback: saved to feedback-local.md`.

## Output to user
Confirm the message was captured, whether the debug log was attached, and that transport is stubbed (not yet transmitted).

## Note
When MCP is live, `@send_feedback` will transmit the message, metadata, and (if the user opted in) the attached debug log directly to Unyak's product team.
