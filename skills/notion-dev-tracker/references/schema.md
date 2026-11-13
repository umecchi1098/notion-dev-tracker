# Notion開発タスクDBの契約

列名は小文字の snake_case を使う。各DBの実際のデータソースIDと案件のepicページIDは、導入先リポジトリの `.notion-dev-tracker.json` で解決する。

スキル内の状態名は意味を表すキー。Notionに保存するときは、設定の `status_values.task_status`、`status_values.epic_status`、`status_values.approval_status`、`status_values.issue_status` を参照する。新規DBでは以下の英語値を初期値にする。既存DBでは状態名を実際の選択肢に対応付ける。

## agent-epic

1レコードを1リポジトリの開発案件として扱う。

| 列名 | Notion型 | 用途 |
| --- | --- | --- |
| name | title | owner/repo などの案件名 |
| repository | url | 正規化したリポジトリのWeb URL |
| summary | rich_text | 案件の概要 |
| status | status | 案件全体の状態 |
| owner | people | 案件担当者 |
| tasks | relation | agent-backlog.epic の逆参照 |
| issues | relation | agent-issue.epic の逆参照 |

## agent-backlog

1レコードを1タスクとして扱う。`epic` を1件指定し、`epic` と `task_id` の組み合わせで特定する。一意性と案件数はスキルが検査する。

| 列名 | Notion型 | 用途 |
| --- | --- | --- |
| name | title | 作業名 |
| task_id | rich_text | 案件内で一意の識別子 |
| epic | relation | agent-epic。逆参照は tasks |
| status | status | タスクの進行状態 |
| priority | number | 小さい数値を優先。空欄は後に扱う |
| execution_os | select | 作業するOS。新規候補は windows、macos、linux |
| agent_thread | rich_text | 担当チャットの識別子 |
| branch | rich_text | 作業ブランチ |
| milestone | rich_text | マイルストーン |
| approval_status | select | 承認根拠の確認状況 |
| dependencies | relation | 先に完了を待つ同一DBのタスク。逆参照は dependents |
| dependents | relation | dependencies の逆参照 |
| next_action | rich_text | 再開時に行う具体的な操作 |
| waiting_reason | rich_text | 承認・割当・依存関係等で待つ理由 |
| report_updated_at | date | 最新の進捗報告日時 |
| tested_sha | rich_text | 実際に検証したコミット |
| integrated_sha | rich_text | 案件の規約に沿って統合を確認したコミット |
| accepted_at | date | 受け入れを確認した日時 |
| issues | relation | agent-issue.tasks の逆参照。タスクはIssueなしでも作成できる |

目的、編集範囲、承認根拠、完了条件、検証結果、移行元とその対応付けはタスク本文に残す。任意項目で根拠が得られない値は空欄にする。`agent_thread` に人の名前を転記するなど、意味の異なる値を流用しない。

## agent-issue

1レコードを1件のアイデア、不具合、改善提案、質問として扱う。`epic` を1件指定し、実際のページIDで特定する。`issue_id` は共通DB内の自動採番IDで、会話からページを探すために使う。案件数と、tasksに関連付けるタスクが同じepicに属することはスキルが検査する。

| 列名 | Notion型 | 用途 |
| --- | --- | --- |
| name | title | 気づきや困りごとの件名 |
| issue_id | unique_id | Notionが自動採番する識別子 |
| epic | relation | agent-epic。逆参照は issues |
| type | select | 新規候補は bug、idea、improvement、question |
| status | status | 受付と調査の状態 |
| tasks | relation | 関連する既存タスクや調査から作成したagent-backlog。逆参照は issues |
| next_action | rich_text | 次に行う調査や確認、必要な追加情報・判断 |
| created_at | created_time | Notionが自動設定する登録日時 |

利用者の報告原文、期待と実際の動作、根拠資料、調査結果、未確定事項、対応案は本文に残す。原文と調査結果を区別し、未知の原因を確定事項として扱わない。1件のIssueと複数タスク、複数Issueと1タスクを関連付けられる。`converted` は受付処理の完了であり、関連タスクの実装や修正の完了を意味しない。

新規DBのissue_idには、利用環境で未使用の接頭辞を、接続先ツールが許容する形式で設定する。例えば `DEV-ISS` を使い、他のデータソースで使用済みなら別の接頭辞を選ぶ。既存DBの接頭辞は保持する。typeの既存日本語値（不具合、アイデア、改善、質問など）は意味が対応すれば保持し、保存するときに実際の選択肢を使う。

## 選択肢の意味と初期値

| 設定マップ | キー | 新規DBの値 | Statusグループ | 既存日本語値の対応例 |
| --- | --- | --- | --- | --- |
| task_status | planned | Planned | to_do | 計画中 |
| task_status | ready | Ready | to_do | 着手可能 |
| task_status | in_progress | In progress | in_progress | 作業中 |
| task_status | blocked | Blocked | in_progress | 中断 |
| task_status | review | In review | in_progress | レビュー待ち |
| task_status | done | Done | complete | 完了 |
| epic_status | backlog | Backlog | to_do | — |
| epic_status | active | Active | in_progress | — |
| epic_status | on_hold | On hold | in_progress | — |
| epic_status | done | Done | complete | — |
| approval_status | approved | Approved | — | 承認済み |
| approval_status | unapproved | Unapproved | — | 未承認 |
| approval_status | unreferenced | No reference | — | 参照なし |
| issue_status | untriaged | Untriaged | to_do | 未調査 |
| issue_status | investigating | Investigating | in_progress | 調査中 |
| issue_status | waiting | Waiting | in_progress | 情報待ち |
| issue_status | converted | Converted | complete | タスク化済み |
| issue_status | dismissed | Dismissed | complete | 見送り |

新規DBの既定状態はタスクが `planned`、案件が `backlog`、Issueが `untriaged`。Issueの未完了は `untriaged`・`investigating`・`waiting`、受付処理済みは `converted`・`dismissed` とする。既存の状態名と意味が一意に対応すればその値を保存する。対応が曖昧なら利用者に確認する。既存の列型、選択肢、リレーション先を変更する必要がある場合は差分を示し、その変更の同意を得る。

`execution_os` は案件で実際に使う値を保存する。既存DBに必要な値がなければ、追加する選択肢を示して対応する。

`approval_status` は記録上の確認状況。作業の実行許可や受け入れは利用者の依頼とリポジトリの規約から確認する。

## リレーションの作成順

1. agent-epic、agent-backlog、agent-issueをtitle列と非リレーション列とともに作り、実際のデータソースIDを取得する。既存DBは再利用する。
2. agent-backlog.epic から agent-epic への双方向リレーションを作り、逆名を tasks にする。
3. agent-backlog.dependencies から同一DBへの双方向リレーションを作り、逆名を dependents にする。
4. agent-issue.epic から agent-epic への双方向リレーションを作り、逆名を issues にする。
5. agent-issue.tasks から agent-backlog への双方向リレーションを作り、逆名を issues にする。
6. 3つのDBを再取得し、型、状態のグループ、参照先と逆列を確認する。

接続先ツールが返す実際のAPI形式に合わせる。双方向リレーションの逆列は同じ関係の一部として扱う。Status型とSelect型を区別し、必要な型や参照先を作れない場合は完了した段階と不足する機能を報告する。

## 案件の設定

[設定例](project-config.example.json) のプレースホルダーを、導入時に解決した値へ置き換える。`source_revision` には配布元で実際に取得したコミットSHAを保存する。認証は利用者のNotion接続を使用する。

`issue_data_source_id` はagent-issueのデータソースUUIDを保存する。Issue未対応の既存設定ではこの項目と `status_values.issue_status` がなくてもタスク操作は利用できる。現状照会ではIssueが未確認であることを明示し、Issue対応を追加するセットアップで実DBと状態マップを補う。配布用の設定例に特定の利用者や案件のNotion IDを埋め込まない。

旧 `.project-tracker.json` のID等は接続先を実際に取得し、対象リポジトリと一致することを確認して引き継ぐ。状態マップがない場合は実スキーマから補う。
