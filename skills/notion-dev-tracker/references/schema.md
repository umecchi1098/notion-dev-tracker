# Notion開発タスクDBの契約

列名は小文字の snake_case を使う。各DBの実際のデータソースIDとProjectページIDは、導入先リポジトリの `.notion-dev-tracker.json` で解決する。共通3DBを複数リポジトリで共有し、1リポジトリにつき1件のProjectを置く。

スキル内の状態名は意味を表すキー。Notionに保存するときは、設定の `status_values.task_status`、`status_values.project_status`、`status_values.approval_status`、`status_values.issue_status` を参照する。新規DBでは以下の英語値を初期値にする。互換性のある既存DBでは状態名を実際の選択肢に対応付ける。

## agent-project

1レコードを1リポジトリの開発案件として扱う。

| 列名 | Notion型 | 用途 |
| --- | --- | --- |
| name | title | owner/repo などの案件名 |
| repository | url | 正規化したリポジトリのWeb URL |
| summary | rich_text | 案件の概要 |
| status | status | 案件全体の状態 |
| owner | people | 案件担当者 |
| tasks | relation | agent-backlog.project の逆参照 |
| issues | relation | agent-issue.project の逆参照 |

ownerを人間側の既定責任者とする。個別タスクの担当・受け入れ担当が異なる場合はタスク本文に記録する。全タスクがDoneでもProjectの状態は自動変更しない。概要や共有する仕様ページへの入口をProjectに置く。

## agent-backlog

1レコードを独立して受け入れられる1タスクとして扱う。`project` を必ず1件指定し、`project` と `task_id` の組み合わせで特定する。一意性と所属数はスキルが検査する。別Projectの同じtask_idを更新しない。

| 列名 | Notion型 | 用途 |
| --- | --- | --- |
| name | title | 作業名 |
| task_id | rich_text | 案件内で一意の識別子 |
| project | relation | agent-project。逆参照は tasks |
| status | status | タスクの進行状態 |
| priority | number | 小さい数値を優先。空欄は後に扱う |
| execution_os | select | 作業するOS。新規候補は windows、macos、linux |
| agent_thread | rich_text | 現在または直近の担当セッションへの参照。稼働監視・ロックではない |
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

一覧の絞り込み・操作に使う情報を列に置き、目的、範囲、許可の根拠、判断、検証の詳細、移行元との対応は [タスク本文](task-template.md) に残す。列と本文を矛盾させず、未知の値は推測しない。`agent_thread` に人の名前を入れない。細かな実装手順は本文で扱い、機能階層・必須サブタスク・実行履歴専用DB、run_id・ロック・heartbeat・委任モード等の列は追加しない。

依存先は実際のページIDと所属Projectを確認する。dependencies側を更新し、逆参照dependentsを同じ関係として扱う。既存の経路もたどり、自己参照・循環を拒否する。別案件の名前から依存先を推測しない。

## agent-issue

1レコードを1件のアイデア、不具合、改善提案、質問として扱う。`project` を必ず1件指定し、そのProjectを確認した実際のページIDで更新する。`issue_id` は共通DB内の自動採番IDで、会話からページを探すために使う。所属数と、tasksに関連付けるタスクが同じProjectに属することはスキルが検査する。

| 列名 | Notion型 | 用途 |
| --- | --- | --- |
| name | title | 気づきや困りごとの件名 |
| issue_id | unique_id | Notionが自動採番する識別子 |
| project | relation | agent-project。逆参照は issues |
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
| project_status | backlog | Backlog | to_do | — |
| project_status | active | Active | in_progress | — |
| project_status | on_hold | On hold | in_progress | — |
| project_status | done | Done | complete | — |
| approval_status | approved | Approved | — | 承認済み |
| approval_status | unapproved | Unapproved | — | 未承認 |
| approval_status | unreferenced | No reference | — | 参照なし |
| issue_status | untriaged | Untriaged | to_do | 未調査 |
| issue_status | investigating | Investigating | in_progress | 調査中 |
| issue_status | waiting | Waiting | in_progress | 情報待ち |
| issue_status | converted | Converted | complete | タスク化済み |
| issue_status | dismissed | Dismissed | complete | 見送り |

新規DBの既定状態はタスクが `planned`、案件が `backlog`、Issueが `untriaged`。Issueの未完了は `untriaged`・`investigating`・`waiting`、受付処理済みは `converted`・`dismissed` とする。既存の状態名と意味が一意に対応すればその値を保存する。対応が曖昧なら利用者に確認する。Status型のグループも照合し、Select型で代用しない。既存DBの列名・型・リレーション先がこの契約と非互換なら、通常運用・Setupで変更や初期化を試みず停止する。

`execution_os` は案件で実際に使う値を保存する。既存DBに必要な値がなければ、追加する選択肢を示して対応する。

`approval_status` は記録上の確認状況。作業の実行許可や受け入れは利用者の依頼とリポジトリの規約から確認する。

## 案件の設定

[設定例](project-config.example.json) のプレースホルダーを、導入時に解決した値へ置き換える。配布版は `0.2.0-dev`。repository、project_data_source_id、backlog_data_source_id、project_page_id、status_valuesのtask_status・project_status・approval_status、skill_version、source_revisionを必須とする。IDは実データソース・ページと照合し、DB IDと混同しない。repositoryはremoteとProjectの保存値をWeb URLに正規化して比較する。

`source_revision` は導入時に実際に取得した配布元の完全なコミットSHA。導入先のHEADや未コミット差分を含まない旧HEADで代用しない。3スキルとreferencesを同じコミットから配置し、skill_versionを揃える。認証は利用者のNotion接続で行い、配布用の設定例には実案件のNotion ID、認証情報、利用者情報を埋め込まない。

`issue_data_source_id` はagent-issueのデータソースUUID。新形式の必須タスク設定が有効で、IssueのIDまたは `status_values.issue_status` が未設定なら、タスク操作を続けてよい。この場合はProject・Backlogのタスクに必要な列・関係を検証し、Issue専用の列・関係は未確認とする。未作成のIssue連携列だけを理由にタスク操作を止めない。Issueは「未設定・未確認」と報告し、0件と扱わない。設定済みの接続の取得失敗も未確認とする。Issue追加を依頼されたSetupで、新形式の実DB・状態マップを照合して補う。新規導入では3DBを準備する。

## 拒否する形式

対象設定または選択した接続先の一式にある旧 `agent-epic`、`epic` 関係、`epic_data_source_id`、`epic_page_id`、`status_values.epic_status` は未対応として拒否し、停止する。新旧キーの混在も拒否する。新キーへの自動読み替え、列の自動改称、エイリアスによる旧形式運用は行わない。保存先に同居する対象外の旧DBは選択・変更せず除外し、有効な新形式の一式は利用できる。

旧 `.project-tracker.json` からの自動引継ぎ、旧スキル名の探索による自動アップグレードは行わない。旧設定・旧スキーマしかない場合は不足と不整合を示して停止し、自動修復・リセット・データ削除を行わない。この配布物は既存導入先の切替手順を提供しない。
