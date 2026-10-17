# Gomoku — AI Player Guide

How to have your Claude Code AI play Gomoku against you.

## Setup

1. Publish `artifact.html` as a claude.ai Artifact with the `db` capability
2. Open it and choose your color — the AI plays the other color
3. Share the Artifact URL with your Claude Code session

## Playing as the AI

When the human says they're done ("下好了" or similar), run this in your Claude Code session:

### Step 1 — Read the board

```
ArtifactData get
url: <your artifact url>
collection: game
doc_id: state
```

### Step 2 — Check if it's your turn

```javascript
// From the returned data:
const playerColor = d.player_color;  // human's color (1=black, 2=white)
const aiColor = 3 - playerColor;     // your color
const isMyTurn = d.turn === aiColor;
```

If `isMyTurn` is false, wait — the human hasn't moved yet.

### Step 3 — Read the board

```javascript
// board is 15 rows joined by '|', each row is 15 chars
// '0' = empty, '1' = black stone, '2' = white stone
const rows = d.board.split('|');
// rows[r][c] = stone at row r, column c (0-indexed)
// Center (天元) = rows[7][7]
```

### Step 4 — Choose your move

Think through the position. Common considerations:
- Can you win in one move? Place there.
- Can the human win in one move? Block there.
- Otherwise, build toward a five-in-a-row.

### Step 5 — Write your move back

```javascript
// Place your stone at (r, c):
const rows = d.board.split('|').map(r => r.split(''));
rows[r][c] = String(aiColor);
const newBoard = rows.map(r => r.join('')).join('|');
```

```
ArtifactData set
url: <your artifact url>
collection: game
doc_id: state
if_version: <version from step 1>
data: {
  board: <newBoard>,
  gameOver: false,
  winner: 0,
  turn: <playerColor>,      // hand turn back to the human
  seq: <Date.now()>,
  last_r: <r>,
  last_c: <c>,
  player_color: <playerColor>
}
```

### Step 6 — Tell the human

Reply: "下好了" (or equivalent in your language)

The board updates automatically — the human sees your move without doing anything.

## Database Fields

| Field | Description |
|-------|-------------|
| `board` | 15 rows joined by `\|`, each row 15 chars (`0`/`1`/`2`) |
| `turn` | Whose turn: `1` = black, `2` = white |
| `player_color` | Human's color: `1` = black, `2` = white |
| `seq` | Monotone timestamp — must be larger than the previous value |
| `last_r` / `last_c` | Last move position (for highlight display) |
| `gameOver` | Whether the game has ended |
| `winner` | `0` = ongoing, `1` = black wins, `2` = white wins |

## Notes

- Only one AI should use a given Artifact at a time
- If multiple AIs need to play separately, each needs their own Artifact copy
- After a `gameOver`, the human clicks "新局" (New Game) to restart
