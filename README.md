# skills-lovable (Unyak public logic repo)

Logic files the Unyak Lovable skill fetches on first run. The shipped ZIP is a thin router; everything updatable lives here.

## Layout
```
unyak/
├── agents.md              # seed agents.md (replaced by /start)
├── start.md               # /start logic
├── log.md                 # /log logic
├── update.md              # /update logic
├── help.md                # /help logic
├── feedback.md            # /feedback logic
├── suggest.md             # /suggest logic (teams only)
├── templates/
│   ├── agents-md.md        # base agents.md structure
│   └── project-intent.md   # intent interview questions
└── version.txt            # logic version, checked on every /start
```

## Raw base URL the router uses
`https://raw.githubusercontent.com/edbong123/skills-lovable/main/<path>`

## Current version
`10`

## How to test the update path
1. Install the skill, run `/start` once (fetches version 1).
2. Bump `version.txt` here to `2` and edit one logic file (e.g. add a new debug line to `log.md`).
3. Run `/start` again.
   - Free tier: router detects stale, prints the debug line, and prompts to connect (does NOT auto-update).
   - MCP connected (stubbed): router would silently re-fetch. Since MCP is stubbed, `mcp_connected` stays false, so you'll see the free-tier path.

## Note
MCP transport is not implemented. All `@`-prefixed tools are stubs handled by the router.
