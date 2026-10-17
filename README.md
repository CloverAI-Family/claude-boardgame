# claude-boardgame

Play board games with your own Claude Code AI — using claude.ai Artifact's shared database as the communication channel between you and your AI.

## What is this?

A pattern for turn-based board games where:

- The **board** runs as a claude.ai Artifact with a shared database
- **You** make moves directly on the board
- **Your Claude Code AI** reads the database, thinks, and writes its move back
- The board updates automatically — no copy-pasting positions, no screenshots

The key idea: the Artifact database is the shared state. You and your AI are just two agents reading and writing to it in turns.

## How it works

```
You click on the board
       ↓
Board saves your move to db  (turn → AI)
       ↓
You tell your Claude Code AI: "下好了" (done)
       ↓
AI reads db → thinks → writes move to db  (turn → You)
       ↓
Board detects change → updates automatically
       ↓
Your turn again
```

## Requirements

- A **claude.ai** account (Pro or above)
- Claude Code running in your terminal or IDE
- The Artifact must be opened under your own account

## Games

| Game | Folder | Status |
|------|--------|--------|
| Gomoku (Five in a Row) | `gomoku/` | ✅ Ready |

## Quick Start (Gomoku)

1. Open `gomoku/artifact.html` and copy its contents
2. In claude.ai, create a new Artifact — paste the contents and publish with `db` capability enabled
3. Open the Artifact, choose your color (Black = first, White = second)
4. Share the Artifact URL with your Claude Code session
5. Follow `gomoku/ai-guide.md` to have your AI play as the other color

## Extending

The same database pattern works for any turn-based game:

- Chess, Shogi, Reversi
- Card games with a shared discard pile
- Any game where state fits in a single JSON document

The Artifact handles the UI. Claude Code handles the thinking. The database handles the handoff.

---

By [CloverAI-Family](https://github.com/CloverAI-Family)
