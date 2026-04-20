---
tags:
  - relaise.
  - アプリ開発
  - 実装状況
created: 2026-04-19
updated: 2026-04-20
version: "1.0"
関連:
  - "[[relaise. 企画書]]"
---

# relaise. — 実装状況

---

## 実装済み機能

### 認証・インフラ
- [x] メール/パスワード認証（Supabase Auth）
- [x] セッション管理・自動ログイン
- [x] Supabase テーブル設計・RLS設定
- [x] サインアップ時の users レコード自動作成（DBトリガー）
- [x] UserProfileManager（プロフィール取得・更新）
- [x] 認証メール日本語化・FRe:x Design App Dev. ブランディング

### NOTE
- [x] フレーズの書き留め・保存（ローカル + Supabase同期）
- [x] タグ入力・表示
- [x] Original / Create バッジ（AI使用フラグ）
- [x] 音声入力（マイクボタン）
- [x] All / Original フィルター
- [x] フレーズ詳細表示（タグ・日時・バッジ）
- [x] 編集・削除（Supabase同期）
- [x] Post ボタン（LYRICSへ放つ）

### LYRICS
- [x] タイムライン表示（Supabaseから全ユーザーのPostを取得）
- [x] プルリフレッシュ対応
- [x] Re:lay数表示・Fine バッジ（黒塗り）
- [x] タップでリレー詳細へ（自分 → ローカル / 他人 → Supabase取得）
- [x] 他人のリレー詳細（LyricsDetailView）：全行・作者名・Re:lay入力

### RE:LAY
- [x] Post / Re:lay タブ切り替え
- [x] リレー詳細（連鎖表示・縦線UI）
- [x] 自分が書いた行だけ黒表示
- [x] いいねボタン（各行）
- [x] Re:lay 入力・送信（Supabase同期）
- [x] 文字数制限：10文字以上50文字未満（空白除く）
- [x] 自分の行を長押しで編集・削除（Supabase同期）
- [x] Fine 確認オーバーレイ・Supabase同期
- [x] isOwner 判定（Post/Re:layで権限分岐）

### プロフィール
- [x] ニックネーム・@ID・アバター表示（Supabaseから取得・保存）
- [x] Post / Re:lay / Fine 統計
- [x] Fine した作品一覧（タップで詳細）
- [x] プロフィール編集（Supabase同期）
- [x] プライバシー設定（is_private）
- [x] 他ユーザーのプロフィール閲覧（作者名タップ）
- [x] @ID：英数字・`_`・`.`のみ使用可能

### コンテンツ安全
- [x] 禁止ワードフィルター（ContentFilter）
- [x] Post・Re:lay時に自動チェック
- [x] 日本人アーティストの歌詞表現を尊重したリスト設計

---

## 次に実装すること

### 🟡 次のフェーズ

| 項目 | 内容 |
|------|------|
| **通知機能** | RE:LAYタブ右上にベルアイコン |
| **通知トリガー** | 自分のPostにRe:layされた / FineになったRe:layに参加していた / さらにRe:layがついた |
| **Re:layタブの実データ** | 多ユーザー化で参加したリレーを Supabase から取得 |
| **Postタブの絞り込み** | Fine済みを除いた進行中のみ表示 |
| **いいねのSupabase同期** | likes テーブルへの保存・集計 |

### 🟢 将来的に検討

| 項目 | 内容 |
|------|------|
| **言葉ツール** | 類語・韻・連想辞書 |
| **フォロー機能** | ユーザーをフォローしてLYRICSを絞り込み |
| **タグ探索** | タグからリレーを発見する画面 |
| **タイムスタンプ証明** | プレミアム機能 |
| **エクスポート** | Fine作品のPDF出力 |
| **Apple Sign In** | メイン認証として追加 |
| **アプリアイコン** | relaise. ブランドアイコン |
| **Android対応** | iOS安定後 |
| **実機テスト** | Apple Developer登録後・長押しUXの検討 |
