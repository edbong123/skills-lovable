# Project Intent (template)

These are the fields agents.md needs. In `/start`, treat them as a **checklist to confirm or fill gaps** after analyzing the project — not a script to read top to bottom. Skip anything the codebase or existing specs already answer; only ask the user about genuine gaps or to confirm an inference.

Fields:
1. **What is being built** — one sentence. Often inferable from routes/components/README.
2. **Who it is for** — the user/audience. Usually needs the human; rarely in code.
3. **Hard constraints** — anything the agent must always or never do. Some are inferable (auth present, payment provider); others need the human.
4. **Stack** — detect from dependencies and config; confirm with the user rather than asking blind.

When asking, ask one at a time, in plain language. Frame as confirmation where you already have a strong inference: "Looks like this is a booking app for small clinics — correct?" rather than "What are you building?"

Write the resolved answers into the **Project Intent** section of agents.md. This block is never overwritten by `/update` or logic updates.
