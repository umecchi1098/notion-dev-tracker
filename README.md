# Notion Dev Tracker

Codex と Claude Code で、開発タスクと Issue を Notion 上で管理するためのスキルです。両方を使う案件では、同じ開発ルールと Notion Project を共有し、タスクの続きから作業できます。

## 新規導入

対象リポジトリを Codex または Claude Code で開き、次の依頼を渡してください。使用するエージェントにはリポジトリの編集権限と、保存先にアクセスできる Notion 接続が必要です。保存先を聞かれたら、親ページか既存 DB のリンクを渡してください。

```markdown
このリポジトリに Notion Dev Tracker を導入してください。
https://github.com/umecchi1098/notion-dev-tracker の main の同じコミットから、skills/ 内の notion-dev-tracker、notion-dev-tracker-setup、notion-dev-tracker-migrate を参照資料ごと取得してください。

現在使っているエージェントに3スキルを配置してください（Codex は .agents/skills/、Claude Code は .claude/skills/）。もう一方も使っていて未導入なら、そちらにも入れるか相談してください。両方に導入するときは同じ配布物を使い、手元の編集と競合する場合は配置前に差分を見せてください。

配置した notion-dev-tracker-setup/SKILL.md をこのセッションで読み、開発ルールと Notion の接続先を設定してください。動作確認のための書き込みは、私が指定した専用のテスト保存先でだけ行ってください。最後に通常スキルで現状を確認し、Project リンク、作成・再利用した DB、配置先と版、取得範囲と件数、未確認事項を教えてください。
```

## 更新

導入済みのリポジトリで、Codex または Claude Code に次の依頼を渡してください。

```markdown
このリポジトリの Notion Dev Tracker を更新してください。
https://github.com/umecchi1098/notion-dev-tracker の main の同じコミットから、skills/ 内の notion-dev-tracker、notion-dev-tracker-setup、notion-dev-tracker-migrate を参照資料ごと取得してください。

Codex の .agents/skills/ と Claude Code の .claude/skills/ を確認し、導入済みの3スキルを更新してください。片側だけ導入済みで、未導入のエージェントも使っている場合は、そちらにも入れるか相談してください。対象の配置先ごとに差分を確認し、手元の編集を上書き・削除する場合は先に見せてください。すべての対象で配布物との一致を確認してから、通常スキルの metadata.version を .notion-dev-tracker.json の skill_version に記録してください。

更新版の Setup に従って開発ルールを共有してください。エージェントを追加するだけなら、既存設定の Project と DB を読み取りで照合してください。設定や DB 構成の更新が必要な場合は、Setup の該当手順を既存の接続先に適用してください。既存 Backlog に implementation_agent 列がなければ追加するか相談してください。配置・ルール・設定に差分がなく、追加する項目もなければ「更新不要」と伝えてください。
```

## エージェントへの依頼例

| やりたいこと | 依頼例 |
| --- | --- |
| 次に着手する仕事を選ぶ | 「このProjectの未完了タスクと未処理Issueを確認して、次に進める候補を教えて」 |
| Issueを調査する | 「このIssueを調査して結果を記録して。対応が必要なら既存タスクに関連付けるか、Plannedで登録して」 |
| タスクを実装する | 「このタスクを本文の受け入れ条件に沿って実装・検証し、レビューに出して」 |
| 複数のタスクを順番に進める | 「この3件を依存順に、このセッションで1件ずつ実装・検証して。各タスクはレビュー待ちで提出して」 |

実装を任せるときは、ReadyやApprovedのタスクから対象を指定し、依頼する作業を伝えてください。マージやデプロイ、データの削除を任せる場合は、その操作も明示してください。

## Notionでの管理

複数のリポジトリで次の3つのDBを共有できます。

| DB | 記録する内容 |
| --- | --- |
| agent-project | リポジトリごとに1件のProject。案件の概要、担当者、状態、資料へのリンク |
| agent-backlog | 個別に検証・受け入れができるタスク。進捗、優先順位、依存関係、引き継ぎ事項 |
| agent-issue | 不具合、アイデア、改善案、質問と、その調査結果 |

Backlog の `implementation_agent`（複数選択）には、実装変更を報告したエージェントを記録します。Codex が実装し Claude Code がレビューした場合は `Codex`、Claude Code も修正した場合は両方が残ります。

タスクやIssueはそれぞれ1件のProjectに属します。複数の案件で同じタスクIDを使っている場合は、ProjectとタスクIDを一緒に指定してください。Issueの更新には対象ページのリンクを使えます。

## 既存タスクの取込み

タスクをファイルで管理している場合は、エージェントにそのファイルを示し、取込み案を依頼してください。対象、項目の対応、重複・競合、依存関係、件数を確認してから取り込みます。Notionを管理元に切り替える時期と元ファイルの扱いは、取込み結果を確認して決めてください。

## スキルと参照資料

- [通常運用](skills/notion-dev-tracker/SKILL.md) · [セットアップ・再セットアップ](skills/notion-dev-tracker-setup/SKILL.md) · [既存タスクの取込み](skills/notion-dev-tracker-migrate/SKILL.md)
- [DBの項目と状態](skills/notion-dev-tracker/references/schema.md) · [リポジトリの設定例](skills/notion-dev-tracker/references/project-config.example.json)
- [着手条件と状態遷移](skills/notion-dev-tracker/references/workflow.md) · [タスク本文と引き継ぎ](skills/notion-dev-tracker/references/task-template.md) · [Issueの調査とタスク化](skills/notion-dev-tracker/references/issues.md)
