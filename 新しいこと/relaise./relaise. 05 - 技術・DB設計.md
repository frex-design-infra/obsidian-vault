---
tags:
  - relaise.
  - アプリ開発
  - 技術設計
  - Supabase
created: 2026-04-19
updated: 2026-04-20
version: "1.0"
関連:
  - "[[relaise. 企画書]]"
---

# relaise. — 技術・DB設計

---

## 技術スタック

| レイヤー | 技術 |
|---------|------|
| **フロントエンド** | SwiftUI（iOS先行、後にAndroid対応） |
| **バックエンド** | Supabase（認証・DB・リアルタイム） |
| **認証** | メール/パスワード認証（実装済み）。Apple Sign In は後から追加予定 |
| **ローカル永続化** | SwiftData（ローカルキャッシュ） |
| **言語資源** | 日本語連想類語辞典 / UniDic / 日本語WordNet / 独自韻データベース |

---

## Supabase テーブル設計（実装済み）

```
users
  id, nickname, handle, avatar_url, is_private(bool), created_at
  ※ サインアップ時にDBトリガーで自動作成
  ※ is_private=true の場合、統計・Fine作品は他ユーザーから非表示

notes
  id, user_id, content, tags(text[]), is_original(bool), created_at

relays
  id, owner_note_id, owner_user_id, is_closed(bool), created_at

relay_lines
  id, relay_id, user_id, content, line_order(int), created_at
  ※ line_order で順序管理

likes
  id, relay_id, line_order, user_id, created_at
  ※ unique(relay_id, line_order, user_id)

notifications
  id, user_id, type, relay_id, from_user_id, is_read, created_at
```

---

## ビュー

```
lyrics_feed（VIEW）
  relay_id, is_closed, created_at, owner_user_id,
  first_line, nickname, handle, line_count
  ※ LYRICSタイムラインの取得に使用
```

---

## RLS ポリシー

| テーブル | ポリシー |
|---------|---------|
| `users` | 全員読み取り可。本人のみ編集 |
| `notes` | 本人のみ CRUD |
| `relays` | 全員読み取り。本人のみ作成・編集 |
| `relay_lines` | 全員読み取り。ログイン済みなら作成 |
| `likes` | 全員読み取り。本人のみ作成・削除 |
