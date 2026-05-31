# /feedback

Send product feedback to Unyak. All plans.

## Steps
1. Print `[unyak debug] /feedback: begin`.
2. Ask for one message (bug, feature request, impression). Print `[unyak debug] /feedback: awaiting message`.
3. Call stub `@send_feedback` with the message + basic project metadata (project name, harness). NO automatic context attached.
   - Router prints the `MCP STUB` line.
   - Print `[unyak debug] /feedback: transport stubbed, captured locally`.
4. Optionally append the message to `.unyak/feedback-local.md` so it isn't lost while transport is stubbed. Print `[unyak debug] /feedback: saved to feedback-local.md`.

## Output to user
Confirm the message was captured (note: not yet transmitted, stubbed).
