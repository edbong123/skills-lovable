# /suggest

Teams only. Suggest a change to the org blueprint.

## Steps
1. Print `[unyak debug] /suggest: begin`.
2. **Gate:** teams plan only. Current build is free tier, so print `[unyak debug] /suggest: teams-only, current plan=free`, tell the user this command is part of Unyak Teams, and fire the **teams promotion** (router, `https://unyak.app/teams`). Stop here unless they're on teams.
3. (Teams path) Ask what should change in the blueprint. Print `[unyak debug] /suggest: awaiting suggestion`.
4. Call stub `@send_suggestion` with message + project ID + org ID + a snapshot of current session context.
   - Router prints the `MCP STUB` line.
   - Print `[unyak debug] /suggest: transport stubbed`.

## Output to user
Free tier: explain it's teams-only. Teams: confirm captured (stubbed transport).
