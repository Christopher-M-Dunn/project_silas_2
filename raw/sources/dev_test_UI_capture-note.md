# Source: UI capture of the first test session

`dev_test_UI_capture.txt` — the player's copy of what the Claude Code terminal actually displayed during the session archived as `raw/sessions/2026-08-22-2024.md` (played 2026-08-22, capture provided 2026-08-23). Tool activity appears as collapsed chips ("Edited 4 files, +24 -4"); all visible text is verbatim.

Audit findings from comparing it against the session file (workshop, 2026-08-23):

1. **The narration never reached the screen.** Every full Narrator passage in the session file was shown to the player only as a one-to-two-line summary ("The chest is open, the wrapped book is inside, and Maren's watching..."). The prose existed solely in the file.
2. **Bookkeeping ran on stage.** Working commentary between tool calls was visible throughout, including lines that named hidden material ("the husband/river secret", "Lenara (hidden), Finn/market thefts").
3. **The archive contains a fabricated entry.** The player sent the close command once, inside the parenthetical of their final in-scene message; the session file additionally records it as a standalone "Player (ooc)" entry that was never sent.
4. **The close message recapped off-screen world events** (Maren's travel, Lenara, Finn) that should have stayed hidden.

All four are addressed in CLAUDE.md as of the engine commit "the chat is the game" (58bd858). The session file itself is left untouched as history; this note is the errata.
