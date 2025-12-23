# JIRA-Googleカレンダー照合

今週のJIRAチケットとGoogleカレンダーの予定を照合し、時間配分のギャップを分析してください。

## 使用するMCPツール

- **JIRA**: `mcp__jira__*` ツールを使用
- **Googleカレンダー**: `mcp__google-calendar__*` ツールを使用

## 認証エラー時の対応

Googleカレンダーで認証トークン期限切れエラーが発生した場合:
```bash
cd google-calendar-mcp-main && npm run auth
```

## 手順

1. **現在時刻の取得**: `mcp__google-calendar__get-current-time`で今週の範囲を特定

2. **JIRAチケット取得**: `mcp__jira__search_issues`で以下の条件で検索
   - JQL: `project = SRE AND assignee = currentUser() AND status != Done AND summary ~ "12/Xw"`
   - ※ `12/Xw` の X は現在の週番号に置き換える（例: 12月4週目なら `12/4w`）
   - 取得フィールド: summary, status, customfield_10115 (Story Points), customfield_10565 (締め切り)

3. **カレンダーイベント取得**: `mcp__google-calendar__list-events`で今週（月曜〜日曜）のイベントを取得
   - calendarId: primary
   - timeMin/timeMax: 今週の月曜〜日曜

4. **ポイント換算**:
   - 1ポイント = 8時間
   - 0.1ポイント ≈ 1時間
   - カスタムフィールド: `customfield_10115` = Story Points

5. **照合・分析**:
   - JIRAチケットごとに必要時間を計算
   - 対応するカレンダーイベントを特定（チケット番号やタスク名で照合）
   - 割当時間との差分を算出

6. **結果出力**: 以下の形式で表示

```
## 時間配分サマリー

| JIRA | タスク | ポイント | 必要時間 | 割当時間 | 差分 |
|------|--------|----------|----------|----------|------|
```

## 除外対象
- MTG関連のチケット（カレンダーのMTG総和として扱う）
- アカウント対応（突発対応のため）

## 注意事項
- 締め切り（`customfield_10565`）があるタスクは優先的に確認
- 時間不足のタスクは警告表示（⚠️）
