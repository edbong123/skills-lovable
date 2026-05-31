# /log

Capture one important thing — a decision, an error, or a direction change.

## Steps
1. Print `[unyak debug] /log: begin`.
2. If the user didn't already describe what happened, ask for one sentence. Print `[unyak debug] /log: awaiting one-sentence input`.
3. **Classify** the sentence as `decision` or `error`. Print `[unyak debug] /log: classified as <decision|error>`.
4. **Append** a structured entry to the matching section of the root `agents.md`:
   ```
   - [<YYYY-MM-DD>] <one-line summary> — <short context>
   ```
   Print `[unyak debug] /log: appended to <Decision|Error> Log`.
5. Increment `log_count` in `unyak/state.json`. Print `[unyak debug] /log: log_count=<n>`.
6. If `log_count == 3`, trigger the "after 3rd /log" promotion (router enforces one-per-session).

## Output to user
One line confirming what was logged and where.
