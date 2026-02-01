# 20_system_design.md

# 1. システム構成

本アプリはクライアントサイドのみで構成されるSPAである。

[ Browser ]
   ↓
[ React Components ]
   ↓
[ useReducer（状態管理） ]
   ↓
[ LocalStorage(JSON) ]

将来的には以下の構成へ拡張可能とする。

[ React ]
   ↓
[ REST API ]
   ↓
[ Oracle DB (TIMESTAMP) ]

そのため、データ形式はAPI転送を前提とした設計とする。

---

# 2. データ設計方針

## 2.1 日付・時刻の扱い

すべての日時は以下の形式で保持する：

- ISO 8601 文字列
- UTC基準
- 例： "2026-01-31T15:30:00.000Z"

理由：

- Oracle TIMESTAMPとの親和性が高い
- C# DateTime (UTC) と相互変換が容易
- REST API標準形式に適合
- 人間可読性がある

---

# 3. エンティティ設計

本アプリでは以下の3エンティティを管理する。

- Task
- Category
- Tag

---

## 3.1 Task

### ■ 定義

Taskは作業項目を表す業務エンティティである。

### ■ データ構造

Task {
  id: string                // UUID
  title: string
  description: string
  categoryId: string | null
  tagIds: string[]
  status: TaskStatus
  createdAt: string         // ISO 8601 (UTC)
  updatedAt: string         // ISO 8601 (UTC)
  completedAt: string | null
}

### ■ TaskStatus

TaskStatus =
  | "TODO"
  | "IN_PROGRESS"
  | "DONE"

※ 将来の拡張を考慮し大文字スネーク風で統一

---

## 3.2 Category

Category {
  id: string
  name: string
  createdAt: string  // ISO 8601 (UTC)
}

---

## 3.3 Tag

Tag {
  id: string
  name: string
  createdAt: string  // ISO 8601 (UTC)
}

---

# 4. リレーション設計

- Task.categoryId → Category.id
- Task.tagIds[] → Tag.id

参照整合性はReducer内で保証する。

---

# 5. アプリケーション状態設計

## 5.1 AppState

AppState {
  tasks: Task[]
  categories: Category[]
  tags: Tag[]
  filter: FilterState
}

---

## 5.2 FilterState

FilterState {
  categoryId: string | null
  tagIds: string[]
  showCompleted: boolean
}

---

# 6. Reducer設計

## 6.1 Task Actions

- ADD_TASK
- UPDATE_TASK
- DELETE_TASK
- CHANGE_TASK_STATUS
- CLEANUP_COMPLETED_TASKS

---

## 6.2 Category Actions

- ADD_CATEGORY
- UPDATE_CATEGORY
- DELETE_CATEGORY

削除時仕様：
- 該当カテゴリを持つTaskのcategoryIdをnullへ変更

---

## 6.3 Tag Actions

- ADD_TAG
- UPDATE_TAG
- DELETE_TAG

削除時仕様：
- 全Taskから該当tagIdを除去する

---

# 7. 完了タスク自動削除仕様

削除条件：

- status = "DONE"
- completedAt が存在する
- 現在UTC時刻 - completedAt >= 24時間

判定はISO文字列を Date オブジェクトへ変換して行う。

---

# 8. LocalStorage保存形式

キー名：

task-app-data

保存JSON構造：

{
  "tasks": Task[],
  "categories": Category[],
  "tags": Tag[]
}

ISO文字列のまま保存する。

---

# 9. 将来API設計との整合性

将来REST API化する場合：

- createdAt / updatedAt / completedAt はそのまま送信可能
- Oracle TIMESTAMPへ直接マッピング可能
- C# DateTime (UTC) へ容易に変換可能

フロントとAPI間のデータ形式変更は不要とする。
