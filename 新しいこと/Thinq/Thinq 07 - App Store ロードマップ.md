---
tags: [Thinq, ロードマップ, 開発]
created: 2026-04-18
関連:
  - "[[Thinq 01 - アプリコンセプト]]"
---

# Thinq — App Store ロードマップ

## 全体像

```
フェーズ1: バックエンド構築
    ↓
フェーズ2: アプリ化（Capacitor）
    ↓
フェーズ3: App Store 提出
    ↓
フェーズ4: Android 対応（Google Play）
```

---

## フェーズ1 — バックエンド構築

**目的：人と繋がるための基盤を作る**

### 使用サービス
| サービス | 役割 | 費用 |
|---|---|---|
| [Supabase](https://supabase.com) | DB・認証・リアルタイム | 無料プランあり |

### やること
- [ ] Supabase アカウント作成・プロジェクト作成
- [ ] DB テーブル設計（`users` / `posts`）
- [ ] 認証設定（メール or Apple Sign In）
- [ ] Supabase JS SDK を `Thinq.html` に組み込む
- [ ] 投稿の保存・読み込みを実データに切り替え
- [ ] タイムラインのリアルタイム更新実装
- [ ] MY_W（感情重みづけ）を投稿内容から動的生成

### DB設計（仮）
```sql
-- ユーザー
users (
  id uuid PRIMARY KEY,
  username text,
  created_at timestamp
)

-- 投稿
posts (
  id uuid PRIMARY KEY,
  user_id uuid REFERENCES users,
  content text,
  emotions jsonb,   -- { anger, anx, sad, fat, calm, joy, excite }
  created_at timestamp
)
```

---

## フェーズ2 — アプリ化（Capacitor）

**目的：今の HTML/CSS/JS をそのまま iOS アプリに変換する**

### 必要なもの
- Mac ✅
- [Node.js](https://nodejs.org) （インストール必要）
- Xcode（Mac App Store から無料）
- CocoaPods（`sudo gem install cocoapods`）

### 手順
- [ ] Node.js インストール
- [ ] Capacitor セットアップ
```bash
npm init -y
npm install @capacitor/core @capacitor/cli
npx cap init
npm install @capacitor/ios
npx cap add ios
```
- [ ] Xcode で iOS プロジェクトを開く
```bash
npx cap copy
npx cap open ios
```
- [ ] 実機テスト（自分の iPhone で確認）
- [ ] TestFlight でベータ配布

---

## フェーズ3 — App Store 提出

**目的：一般公開**

### 必要なもの
| 項目 | 詳細 | 費用 |
|---|---|---|
| Apple Developer Account | [developer.apple.com](https://developer.apple.com) | 年 $99（約15,000円） |
| アプリアイコン | 1024×1024px | - |
| スクリーンショット | iPhone 各サイズ | - |

### 手順
- [ ] Apple Developer Account 登録
- [ ] App Store Connect でアプリ登録
- [ ] アイコン・スクリーンショット準備
- [ ] Xcode から Archive → 提出
- [ ] 審査（通常1〜3日）

---

## フェーズ4 — Android 対応（Google Play）

**目的：Android ユーザーにも届ける**

Capacitor を使っているので追加の開発コストはほぼなし。

### 必要なもの
| 項目 | 費用 |
|---|---|
| Google Play Console | 初回 $25（約4,000円）のみ |
| Android Studio | 無料 |

### 手順
- [ ] Android Studio インストール
- [ ] Capacitor に Android を追加
```bash
npm install @capacitor/android
npx cap add android
npx cap open android
```
- [ ] Google Play Console でアプリ登録
- [ ] 審査・公開

---

## 現在の状況

| 項目 | 状態 |
|---|---|
| UI / アニメーション | ✅ 完成 |
| Aurora Ring エンジン | ✅ 完成 |
| チュートリアル | ✅ 完成 |
| 音声入力 | ✅ 完成 |
| バックエンド | ⬜ 未着手 |
| 認証 | ⬜ 未着手 |
| Capacitor 化 | ⬜ 未着手 |
| App Store 提出 | ⬜ 未着手 |

---

## 参考リンク

- [Supabase ドキュメント](https://supabase.com/docs)
- [Capacitor 公式](https://capacitorjs.com)
- [Apple Developer](https://developer.apple.com)
- [App Store Connect](https://appstoreconnect.apple.com)
- [Google Play Console](https://play.google.com/console)
