# claude-boardgame

用 claude.ai Artifact 的共用資料庫，和你的 Claude Code AI 對弈。

> ⚠️ **token 消耗提醒**：此專案每回合需要 AI 讀取資料庫、計算棋局、寫回結果，比一般任務消耗更多 token。  
> 黑白棋（Reversi）尤其明顯——每步需掃描全盤計算合法落子。  
> 建議在 **Max 訂閱方案**下使用；一般訂閱（Pro）玩幾局後可能明顯感受到額度消耗。

## 這是什麼？

讓你在 claude.ai Artifact 棋盤上，和自己的 Claude Code AI 輪流對弈的框架。

- **棋盤**：運行在 claude.ai Artifact，有共用資料庫
- **你**：直接在棋盤上點擊落子
- **AI**：讀取資料庫、思考、寫回棋步
- **棋盤**：自動偵測更新，不需截圖或複製貼上

核心概念：Artifact 資料庫是共用狀態，你和 AI 只是輪流讀寫的兩個玩家。

## 運作流程

```
你在棋盤點擊落子
       ↓
棋盤儲存你的棋步到 db（輪到 AI）
       ↓
你告訴 Claude Code AI：「下好了」
       ↓
AI 讀取 db → 思考 → 寫回棋步（輪到你）
       ↓
棋盤偵測到變更 → 自動更新
       ↓
換你了
```

## 需求

- **claude.ai** 帳號（Pro 以上；建議 Max 以減少額度壓力）
- Claude Code 在終端機或 IDE 中運行
- Artifact 必須在你自己的帳號下開啟

## 遊戲

| 遊戲 | 資料夾 | 狀態 |
|------|--------|------|
| 五子棋（Gomoku） | `gomoku/` | ✅ 可用 |
| 黑白棋（Reversi） | `reversi/` | ✅ 可用 |

## 快速開始（五子棋）

1. 開啟 `gomoku/artifact.html`，複製內容
2. 在 claude.ai 建立新 Artifact——貼上內容並啟用 `db` 功能後發布
3. 開啟 Artifact，選擇顏色（黑子先手，白子後手）
4. 將 Artifact URL 分享給你的 Claude Code session
5. 依照 `gomoku/ai-guide.md` 讓 AI 扮演另一方

## 快速開始（黑白棋）

1. 開啟 `reversi/artifact.html`，複製內容
2. 在 claude.ai 建立新 Artifact——貼上內容並啟用 `db` 功能後發布
3. 開啟 Artifact，選擇顏色
4. 將 Artifact URL 分享給你的 Claude Code session
5. 依照 `reversi/ai-guide.md` 讓 AI 扮演另一方

---

<details>
<summary>English</summary>

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

- A **claude.ai** account (Pro or above; **Max recommended** to reduce token pressure)
- Claude Code running in your terminal or IDE
- The Artifact must be opened under your own account

> ⚠️ **Token usage note**: Each AI turn requires reading the database, analyzing the board, and writing back a move — significantly more expensive than typical tasks. Reversi is especially heavy (full board scan per turn). Max plan is recommended; Pro users may notice meaningful token consumption after a few games.

## Games

| Game | Folder | Status |
|------|--------|--------|
| Gomoku (Five in a Row) | `gomoku/` | ✅ Ready |
| Reversi (Othello) | `reversi/` | ✅ Ready |

## Quick Start (Gomoku)

1. Open `gomoku/artifact.html` and copy its contents
2. In claude.ai, create a new Artifact — paste the contents and publish with `db` capability enabled
3. Open the Artifact, choose your color (Black = first, White = second)
4. Share the Artifact URL with your Claude Code session
5. Follow `gomoku/ai-guide.md` to have your AI play as the other color

## Quick Start (Reversi)

1. Open `reversi/artifact.html` and copy its contents
2. In claude.ai, create a new Artifact — paste the contents and publish with `db` capability enabled
3. Open the Artifact, choose your color
4. Share the Artifact URL with your Claude Code session
5. Follow `reversi/ai-guide.md` to have your AI play as the other color

## Extending

The same database pattern works for any turn-based game: Chess, Shogi, card games with a shared discard pile — anything where state fits in a single JSON document. The Artifact handles the UI, Claude Code handles the thinking, and the database handles the handoff.

</details>

---

By [CloverAI-Family](https://github.com/CloverAI-Family)
