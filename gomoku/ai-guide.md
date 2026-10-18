# 五子棋 AI 操作指南

如何讓你的 Claude Code AI 和你對弈五子棋。

## 設定

1. 將 `artifact.html` 以啟用 `db` 功能的方式發布為 claude.ai Artifact
2. 開啟 Artifact，選擇你的顏色——AI 扮演另一方
3. 將 Artifact URL 分享給你的 Claude Code session

## 操作流程

當玩家說「下好了」時，在 Claude Code 執行以下步驟：

### 步驟 1 — 讀取棋盤

```
ArtifactData get
url: <你的 artifact url>
collection: game
doc_id: state
```

### 步驟 2 — 確認是否輪到 AI

```javascript
const playerColor = d.player_color;  // 玩家顏色（1=黑，2=白）
const aiColor = 3 - playerColor;     // AI 顏色
const isMyTurn = d.turn === aiColor;
```

若 `isMyTurn` 為 false，等待——玩家尚未落子。

### 步驟 3 — 解析棋盤字串

```javascript
// board 是 15 行以 '|' 分隔的字串，每行 15 個字元
// '0' = 空格，'1' = 黑子，'2' = 白子
const rows = d.board.split('|');
// rows[r][c] = 第 r 行第 c 列（0-indexed）
// 天元 = rows[7][7]
```

### 步驟 4 — 選擇落子位置

思考棋局：
- 我能一步獲勝嗎？落在那裡。
- 對方能一步獲勝嗎？封住那裡。
- 否則，朝五連方向發展。

### 步驟 4.5 — 驗算落子位置（必做）

選定 `(r, c)` 後，用 Bash 確認該位置確實為空格，再寫回：

```bash
python3 -I -c "
import sys
board, r, c = sys.argv[1], int(sys.argv[2]), int(sys.argv[3])
rows = board.split('|')
print(f'行{r}完整內容：{rows[r]}')
print(f'({r},{c}) = {rows[r][c]}（應為 0）')
" "BOARD_STRING" R C
```

將 `BOARD_STRING`、`R`、`C` 替換為實際值。確認輸出顯示 `= 0` 後再進行步驟 5。

### 步驟 5 — 寫回棋步

```javascript
const rows = d.board.split('|').map(r => r.split(''));
rows[r][c] = String(aiColor);
const newBoard = rows.map(r => r.join('')).join('|');
```

```
ArtifactData set
url: <你的 artifact url>
collection: game
doc_id: state
if_version: <步驟 1 取得的 version>
data: {
  board: <newBoard>,
  turn: <playerColor>,
  seq: <Date.now()>,
  last_r: <r>,
  last_c: <c>,
  player_color: <playerColor>
}
```

### 步驟 6 — 告知玩家

回覆：「下好了」

棋盤自動更新，玩家不需做任何操作。

## 資料庫欄位

| 欄位 | 說明 |
|------|------|
| `board` | 15 行以 `\|` 分隔，每行 15 字元（`0`/`1`/`2`） |
| `turn` | 輪到誰：`1` = 黑，`2` = 白 |
| `player_color` | 玩家顏色：`1` = 黑，`2` = 白 |
| `seq` | 單調遞增時間戳——必須大於前一個值 |
| `last_r` / `last_c` | 最後落子位置（用於高亮顯示） |

## 注意事項

- 每個 Artifact 同時只能有一個 AI 使用
- 棋盤自行偵測五連——不要寫入 `gameOver` 或 `winner`
- 遊戲結束後，玩家點擊「新局」重新開始

---

<details>
<summary>English</summary>

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

Think through the position:
- Can you win in one move? Place there.
- Can the human win in one move? Block there.
- Otherwise, build toward a five-in-a-row.

### Step 4.5 — Verify the target position (required)

After choosing `(r, c)`, confirm with Bash that the cell is empty before writing back:

```bash
python3 -I -c "
import sys
board, r, c = sys.argv[1], int(sys.argv[2]), int(sys.argv[3])
rows = board.split('|')
print(f'Row {r}: {rows[r]}')
print(f'({r},{c}) = {rows[r][c]} (should be 0)')
" "BOARD_STRING" R C
```

Replace `BOARD_STRING`, `R`, `C` with actual values. Confirm the output shows `= 0` before proceeding.

### Step 5 — Write your move back

```javascript
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
  turn: <playerColor>,
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

## Notes

- Only one AI should use a given Artifact at a time
- The board detects five-in-a-row itself — do not write `gameOver` or `winner`
- After the game ends, the human clicks "新局" (New Game) to restart

</details>
