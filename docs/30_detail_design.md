# 30_detail_design.md

# 詳細設計

本書では、画面設計・UI/UX設計・コンポーネント設計・状態管理・処理フローを定義する。  
本設計はエンタープライズ志向（将来API化前提）で構成する。

---

# 1. 画面設計

## 1.1 画面一覧

| 画面ID | 画面名 | 概要 |
|--------|--------|------|
| SCR-01 | タスク一覧画面 | タスクの表示・登録・編集・完了管理 |
| SCR-02 | カテゴリ管理モーダル | カテゴリの追加・編集・削除 |
| SCR-03 | タグ管理モーダル | タグの追加・編集・削除 |

※ 本アプリはSPA構成とし、画面遷移は行わずモーダル制御とする。

---

# 2. SCR-01 タスク一覧画面

## 2.1 レイアウト構成

-------------------------------------------------
 ヘッダー
-------------------------------------------------
 フィルタエリア
  - カテゴリ選択
  - タグ選択
  - ステータス選択
-------------------------------------------------
 タスク入力エリア
  - タイトル
  - 説明
  - カテゴリ選択
  - タグ選択（複数）
  - 登録ボタン
-------------------------------------------------
 タスク一覧表示エリア（付箋形式）
-------------------------------------------------

---

# 3. コンポーネント設計

## 3.1 コンポーネント構成

App  
 ├── Header  
 ├── FilterPanel  
 ├── TaskInputForm  
 ├── TaskList  
 │     └── TaskCard  
 ├── CategoryModal  
 └── TagModal  

## 3.2 責務定義

| コンポーネント | 責務 |
|----------------|------|
| App | 全体状態管理、LocalStorage連携 |
| FilterPanel | フィルタ条件変更 |
| TaskInputForm | タスク新規登録 |
| TaskList | タスク一覧描画 |
| TaskCard | 単一タスク表示・編集 |
| CategoryModal | カテゴリ管理 |
| TagModal | タグ管理 |

---

# 4. 状態管理設計

## 4.1 AppState

type AppState = {  
  tasks: Task[]  
  categories: Category[]  
  tags: Tag[]  
}

## 4.2 Action定義

type Action =  
  | { type: "ADD_TASK"; payload: Task }  
  | { type: "UPDATE_TASK"; payload: Task }  
  | { type: "DELETE_TASK"; payload: string }  
  | { type: "CHANGE_TASK_STATUS"; payload: { id: string; status: TaskStatus } }  
  | { type: "ADD_CATEGORY"; payload: Category }  
  | { type: "UPDATE_CATEGORY"; payload: Category }  
  | { type: "DELETE_CATEGORY"; payload: string }  
  | { type: "ADD_TAG"; payload: Tag }  
  | { type: "UPDATE_TAG"; payload: Tag }  
  | { type: "DELETE_TAG"; payload: string }

---

# 5. 業務ロジック詳細

## 5.1 タスク登録

### 必須項目
- title（1文字以上）

### 処理内容
1. id をUUIDで生成  
2. createdAt に現在UTC ISO8601文字列を設定  
3. updatedAt に同値を設定  
4. status 初期値は "TODO"  
5. completedAt は null  

---

## 5.2 タスク更新

- updatedAt を現在UTC ISO文字列に更新  

---

## 5.3 ステータス変更

### DONEに変更時
- completedAt に現在UTC ISO文字列を設定  

### TODO / IN_PROGRESS に戻した場合
- completedAt を null に戻す  

---

## 5.4 完了タスクの自動削除

アプリ起動時に実行。

条件：

status === "DONE"  
かつ  
completedAt から24時間以上経過  

該当タスクは削除する。

---

# 6. LocalStorage設計

## 6.1 保存キー

taskapp_state_v1

## 6.2 保存仕様

- state変更時 useEffect にて保存  
- 初期化時に読み込み  
- JSONパースエラー時は初期状態で起動  

---

# 7. UI/UX設計

## 7.1 タスクカード表示ルール

| ステータス | 表示仕様 |
|------------|----------|
| TODO | 通常表示 |
| IN_PROGRESS | 強調色表示 |
| DONE | グレーアウト表示 |

---

## 7.2 編集方式

- TaskCardクリックで編集モード  
- インライン編集  
- Enterキーで保存  
- Escキーでキャンセル  

---

# 8. バリデーション設計

| 項目 | ルール |
|------|--------|
| title | 必須、100文字以内 |
| description | 1000文字以内 |
| categoryId | 任意 |
| tagIds | 任意 |

---

# 9. エラー処理

- LocalStorage読み込み失敗 → 初期状態で起動  
- JSONパース失敗 → 初期状態で起動  
- 不正ステータス値検出 → TODOにフォールバック  

---

# 10. 将来API化を考慮した設計方針

- IDはUUID文字列  
- 日付はISO8601 UTC文字列  
- ステータスは業務コード値（大文字）  
- クライアントとAPI間はJSON形式  
- フロントとDBの型一致は前提としない（API層で変換）  

---
