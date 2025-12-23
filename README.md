# JIRA-Calendar Tasks

Claude CodeのSlash CommandとMCPを使って、JIRAチケットとGoogleカレンダーの予定を照合・分析するツールです。

## 機能

- 今週のJIRAチケットとGoogleカレンダーの予定を自動照合
- Story Pointsと割り当て時間のギャップを分析
- 時間不足のタスクを警告表示

## セットアップ

### 1. MCP設定

`.mcp.example.json`を`.mcp.json`にコピーして、認証情報を設定してください。

```bash
cp .mcp.example.json .mcp.json
```

### 2. MCP Servers

このプロジェクトは以下のMCPサーバーを使用します：

| サーバー | 用途 |
|---------|------|
| [Atlassian MCP](https://mcp.atlassian.com) | JIRA連携 |
| [@cocal/google-calendar-mcp](https://www.npmjs.com/package/@cocal/google-calendar-mcp) | Googleカレンダー連携 |

### 3. Google Calendar認証

初回またはトークン期限切れ時：

```bash
cd google-calendar-mcp-main && npm run auth
```

## 使い方

Claude Codeで以下のSlash Commandを実行：

```
/jira-calendar-sync
```

### 出力例

```
## 時間配分サマリー

| JIRA | タスク | ポイント | 必要時間 | 割当時間 | 差分 |
|------|--------|----------|----------|----------|------|
| SRE-123 | 機能A実装 | 0.5 | 4h | 2h | -2h ⚠️ |
| SRE-124 | バグ修正 | 0.2 | 1.6h | 2h | +0.4h |
```

## ポイント換算

- 1ポイント = 8時間
- 0.1ポイント ≈ 1時間

## 除外対象

- MTG関連のチケット（カレンダーのMTG総和として扱う）
- アカウント対応（突発対応のため）

## ファイル構成

```
.
├── .claude/
│   └── commands/
│       └── jira-calendar-sync.md   # Slash Command定義
├── .mcp.example.json               # MCP設定サンプル
├── .mcp.json                       # MCP設定（gitignore対象）
└── google-calendar-mcp-main/       # Google Calendar MCPサーバー
```

## ライセンス

MIT
