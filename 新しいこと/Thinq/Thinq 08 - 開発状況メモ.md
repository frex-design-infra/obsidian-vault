---
tags: [Thinq, 開発, 進捗]
created: 2026-04-18
updated: 2026-04-18
関連:
  - "[[Thinq 01 - アプリコンセプト]]"
---

# Thinq — 開発状況メモ

## ✅ 完成済み

### UI / 体験
- Aurora Ring エンジン（200スパイク・7感情ゾーン・成長システム）
- タイムライン・日記画面・MY RING・詳細画面
- チュートリアル（Thinq Log / Aurora Ring）
- スタート画面（はじめる → レインボーアニメ → スプラッシュ → メイン）
- 一度きりの出会い（読んだ Log はタイムラインから消えるアニメ付き）

### バックエンド（Supabase）
- 匿名認証（登録不要・起動で即使える）
- `posts` テーブル設計・RLS設定
- シードデータ投入（60件・各ユーザーのリング成長もランダム）
- 投稿の保存（`savePost`）
- タイムライン読み込み（`loadTimeline`）
- 自分の投稿読み込み（`loadMyPosts`）
- 感情推定（`estimateEmotions` — キーワードベース）

---

## 🔧 残タスク

### 優先度：高
- [x] **削除をSupabaseと連動**
  - `deletePost(postId)` 関数を追加、詳細画面・MY RINGスワイプの両方から呼び出し
  - 楽観的更新（UI即反映、DB削除は非同期）

- [x] **シードデータのuser_id をランダムUUIDに変更**
  - 19名のペルソナに分散（会社員・就活生・親・管理職・フリーランス・高齢者など）
  - `author_ring_count` もユーザーごとに統一
  - `views` も投稿日からの経過日数に合わせてリアルな値に修正（最大200以内）
  - Supabase SQL エディタで `thinq_seed.sql` を再実行すれば反映

### 優先度：中
- [x] **リアルタイム更新**
  - WebSocket（Supabase Realtime）はTIMED_OUTのためポーリング方式に変更
  - 20秒ごとに `pollNewPosts()` を実行、ページ読み込み時刻より新しい投稿を取得
  - 自分の投稿・既読投稿は除外
  - タイムライン画面にいるとき：上からフェードイン（0.7s）で行を追加
  - 別画面にいるとき：`POSTS` 配列だけ更新、戻ったとき自動反映
  - `loadTimeline()` でも自分の投稿をDBレベルで除外（`.neq('user_id', user.id)`）

- [x] **句読点補正（感情推定の精度向上 第1弾）**
  - `！` が1つ以上 → excite を加算（元気です！→ 喜び × ワクワク）
  - `！` なし ＋ 強がり系ワード（元気/大丈夫/平気/普通/なんでもない）→ anx を加算
  - 将来：OpenAI API or 感情辞書でさらに精度アップ

- [x] **共鳴ルール実装**
  - `getMyTopEmotions()` — 直近5件の投稿から自分のtop3感情を算出
  - `checkResonates()` — 波形タイプ別の条件で共鳴判定（詳細はThinq 06 - ロジックルール設計参照）
  - `loadMyPosts` 完了後にタイムライン全投稿の共鳴を再評価（初回ロード順序問題の対処）

- [x] **viewsのインクリメント**
  - 他人の詳細を開いたときだけ `incrementViews(post)` を呼び出し
  - ローカル即反映（楽観的）+ Supabase RPC でatomic更新
  - ⚠️ Supabase SQL Editor で以下を要実行：
    ```sql
    CREATE OR REPLACE FUNCTION increment_views(post_id uuid)
    RETURNS void AS $$
      UPDATE posts SET views = views + 1 WHERE id = post_id;
    $$ LANGUAGE sql SECURITY DEFINER;
    ```

### 優先度：低（App Store 提出前）
- [x] **Capacitor セットアップ**
  - `~/Documents/thinq-app/` にプロジェクト作成
  - `Thinq.html` → `www/index.html` としてコピー
  - `npm install @capacitor/core @capacitor/cli`
  - `npx cap init Thinq jp.flexdesign.thinq --web-dir www`
  - `npm install @capacitor/ios && npx cap add ios`
  - Xcode で署名設定（Personal Team: frex.design.2022@gmail.com）
  - **iPhone7 SE (2) 実機で起動確認 ✅**

- [ ] **Apple Developer Account 登録**（年 $99）

- [ ] **App Store Connect 登録**
  - アイコン（1024×1024px）
  - スクリーンショット（iPhone各サイズ）
  - App Store 説明文（Thinq 09 - App Store 説明メモ参照）

- [ ] **StoreKit（課金）実装**
  - 無料：投稿・タイムライン閲覧
  - プレミアム：Ring エフェクト全解放・詳細感情分析（350円/月・3日無料）

- [ ] **既読管理をSupabaseに移行**（Capacitor化のタイミングで）
  - 現状：`thinq_seen`（既読post IDの配列）をlocalStorageで管理
  - 問題：機種変更・アプリ再インストール・Safari履歴消去でリセットされ、一度読んだ投稿が再出現する可能性がある
  - 解決策：`seen_posts (user_id, post_id)` テーブルをSupabaseに作成し、匿名ユーザーIDに紐づけて管理
  ```sql
  CREATE TABLE seen_posts (
    user_id uuid REFERENCES auth.users,
    post_id uuid REFERENCES posts,
    seen_at timestamptz DEFAULT now(),
    PRIMARY KEY (user_id, post_id)
  );
  ```

---

## 📋 Supabase 情報

| 項目 | 値 |
|---|---|
| Project URL | `https://znfzucxuovcuuvozsbdm.supabase.co` |
| テーブル | `posts` |
| 認証方式 | Anonymous Auth |

### posts テーブル構造
```
id              uuid (PK)
user_id         uuid
content         text
emotions        jsonb
label           text
colors          jsonb
amp / freq / speed / phase  float
views           integer
author_ring_count  integer
created_at      timestamptz
```

---

## 🗺 ロードマップ（全体）

詳細はThinq 07 - App Store ロードマップ参照

```
フェーズ1: バックエンド構築  ← ほぼ完了
    ↓
フェーズ2: Capacitorでアプリ化
    ↓
フェーズ3: App Store 提出
    ↓
フェーズ4: Android 対応
```
