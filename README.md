# VS Code：日本語Markdown執筆・プレビュー最適化ガイド

本ドキュメントは、**要件定義・設計書を日本語Markdownで統一して書くための  事前準備用ガイド**である。

今後の方針として、

- 要件定義書・仕様書・設計書はMarkdownで作成する
- Gitで管理する
- AIレビュー・AI生成を前提とする

ことを目的とする。

---

## 1. 前提環境

- Visual Studio Code
- 日本語表示が有効になっていること
- Gitが利用できること

**日本語化**
- 拡張機能検索で
`Japanese Language Pack`
- 発行者：Microsoft
---

**Gitのインストール**
- [Git公式サイト](https://git-scm.com/install/windows)よりインストール
- 「PATHに追加する」を選択
---

## 2. 必須拡張機能

Markdownを書く・読むために **全員が入れる拡張機能**。

### 2.1 Markdown All in One

Markdown編集を補助する基本拡張で以下に対応。

- 見出し・箇条書きの入力補助
- 目次（TOC）の自動生成
- 見出し構造の整理

**インストール**
- 拡張機能検索で  
  `Markdown All in One`
- 発行者：Yu Zhang

---

### 2.2 Markdown Preview Enhanced

Markdownを **読みやすく表示するための拡張**。

**できること**
- 日本語が崩れにくいプレビュー
- 表・箇条書きが綺麗に表示される
- 将来的にPDF出力も可能

**インストール**
- 拡張機能検索で  
  `Markdown Preview Enhanced`
- 発行者：Yiyi Wang

---

### 2.3 Draw.io Integration

システム構成図・アーキテクチャ図を作るための拡張。

**用途**
- システム構成図
- コンポーネント関係図

**インストール**
- 拡張機能検索で  
  `Draw.io Integration (diagrams.net)`
- 発行者：Henning Dieterichs

---

### 2.4 Japanese Word Handler

日本語文章を書くときの操作性を改善する拡張。

**用途**
- 日本語として自然なカーソル移動
- 削除・選択操作がしやすくなる

**インストール**
- 拡張機能検索で  
  `Japanese Word Handler`
- 発行者：Suguru Yamamoto
---

## 3. Markdownプレビューの使い方

### 3.1 プレビューを開く方法

Markdownファイルを開いた状態で：

- Windows：Ctrl + Shift + V
- Mac：Cmd + Shift + V

右側にプレビューが表示されればOK。

---

## 4. 日本語表示を読みやすくする設定

以下は **全員共通で設定することを推奨**。

### 4.1 設定方法

- 設定画面を開く
- 右上の「settings.json を開く」を選択
- 以下を追記する

### 4.2 推奨設定内容

```
{
  "editor.wordWrap": "on",
  "editor.wordWrapColumn": 1,
  "markdown-preview-enhanced.previewTheme": "github-light.css",
  "markdown-preview-enhanced.fontFamily": "Meiryo, Hiragino Kaku Gothic ProN, Noto Sans JP, sans-serif",
  "markdown-preview-enhanced.fontSize": 14,
  "markdown-preview-enhanced.lineHeight": 1.8,
  "git.enableSmartCommit": true,
  "git.autofetch": true,
  "git.confirmSync": false
}
```

※ 行間が広めの方が日本語は読みやすい。

---

## 5. Markdownでの資料作成ルール

### 5.1 見出しを必ず使う

- 見出しで構造を作る
- 文章をダラダラ書かない

### 5.2 箇条書きは1項目1意味

悪い例：
- ユーザー管理（登録・編集・削除）

良い例：
- ユーザー登録
- ユーザー編集
- ユーザー削除

---

## 6. 図と文章の役割分担

- 図：構成・関係性
- 文章：意味・理由・制約

**図に説明を書きすぎないこと。**

---

## 7. 推奨ドキュメント構成

docs フォルダ配下に以下を作成する。

| ファイル名                  | 内容                             |
| ---------------------- | ------------------------------ |
| `00_overview.md`       | プロジェクト概要、対象範囲、全体像、用語定義         |
| `10_requirements.md`   | 機能要件、非機能要件、制約条件                |
| `20_system_design.md`  | システム構成、データ設計、API設計             |
| `30_detail_design.md`  | 詳細機能設計、UI/UX設計、外部連携設計          |
| `40_implementation.md` | 実装方針、開発環境、コーディングルール、ブランチ戦略     |
| `50_testing.md`        | テスト計画、テスト種類、AI活用               |
| `60_code_review.md`    | コードレビュー、品質管理指標                 |
| `70_deployment.md`     | デプロイ手順、運用監視、保守・改善              |
| `80_ai_guidelines.md`  | AI生成物管理、ライセンス、学習データ、フィードバックループ |
| `glossary.md`          | 用語集                            |
| `architecture.drawio`  | システム構成図、概念図（Draw.ioで作成）        |

---

## 8. Gitでのリポジトリ管理
※git configは設定しておく必要があります。
```
git config --global user.name "m-matsumoto"
git config --global user.email "m-matsumoto@wanbishi.co.jp"
```

- Git上にリポジトリを作成しておく。
- Git初期化
```
git init
```
- コミット
```
git add .
git commit -m "Initial commit"
```
- リモートリポジトリを追加
```
git remote add origin https://github.com/mitsuo-matsumoto/AI-DrivenDevelopment.
```
- プッシュ
```
git branch -M main
git push -u origin main
```

## 8. まとめ

- Markdownは特別な技術ではないため、チャレンジしてみる
- ルールを追加したい場合は管理職・チームリーダーへ相談する
- AI駆動開発に向けた準備として、全員がMarkdownに慣れること

### 8.1 テスト設計を先にやるメリット
- 要件の抜け漏れが炙り出せる
例：
完了→戻す時 completedAt は？
カテゴリ削除時、紐づくタスクは？
タグ削除時は？
同名カテゴリは許可？
テストを書こうとすると、
「あれ？これ仕様決めてない」が必ず出ます。
これが一番の価値。
- reducer設計が強くなる
テスト観点を作ると、
純粋関数として書く
副作用を分離する
初期化ロジックを整理する
設計が一段綺麗になります。
- AIにコードを書かせる精度が上がる
今あなたはAI駆動開発をやっていますよね。
テスト設計があると：
「この仕様を満たす実装を書いて」
と明確に指示できる。
曖昧な実装にならない。

## ドキュメントの読み方

1. README.md（このファイル）
2. docs/00_overview.md（概念設計・フロー）
3. docs/10_requirements.md（要求事項）
4. docs/20_system_design.md（デザイン　→　Figmaも使うか検討）
5. design/figma_export（figmaのデザイン結果）
