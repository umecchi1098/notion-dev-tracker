# Notion Dev Tracker

Codexと一緒に、開発タスクとIssueをNotionで管理するためのスキルです。リポジトリごとにProjectを選び、作業の計画から実装、検証、引き継ぎまでをタスクに記録します。

## 導入手順

1. 導入先のリポジトリをCodexで開き、Notionを接続します。Codexにはリポジトリの編集権限、Notionの接続には保存先の閲覧・編集権限が必要です。
2. 次のプロンプトをコピーしてCodexに渡します。
3. 保存先を聞かれたら、新しいDBを作る親ページ、または既存のDBのリンクを渡します。
4. 返ってきたProjectリンクを開き、対象の案件と、作成または再利用されたDBを確認します。

```markdown
このリポジトリにNotion Dev Trackerをセットアップしてください。

配布元は https://github.com/umecchi1098/notion-dev-tracker です。mainから次の3ディレクトリを配下のファイルごとまとめて取得してください。

- skills/notion-dev-tracker/
- skills/notion-dev-tracker-setup/
- skills/notion-dev-tracker-migrate/

導入先の指示と手元の変更を確認し、3スキルを .agents/skills/ に同名で配置してください。既存の編集を上書きする場合は、先に差分を示して確認してください。

配置した .agents/skills/notion-dev-tracker-setup/SKILL.md をこのセッションで読み、私のNotion接続で実行してください。保存先の指定が必要なら、新しいDBを作る親ページか既存DBのリンクを聞いてください。書込み検証を行うのは、私が専用のテスト保存先を指定した場合だけです。

セットアップ後はnotion-dev-trackerを読み、このProjectの現状を確認してください。Projectリンク、作成・再利用したDB、配置ファイル、バージョン、取得範囲と件数を示してください。確認できなかった項目があれば、その内容も教えてください。今回の依頼はセットアップと現状確認までです。
```

## 更新手順

導入済みのリポジトリをCodexで開き、次のプロンプトを渡します。

```markdown
このリポジトリのNotion Dev Trackerを更新してください。

https://github.com/umecchi1098/notion-dev-tracker のmainから、skills/ 配下の notion-dev-tracker、notion-dev-tracker-setup、notion-dev-tracker-migrate をまとめて取得してください。

配下のファイルも含めて .agents/skills/ の同名ディレクトリと比べ、差分を反映してください。手元の編集を上書き・削除する可能性があれば、その箇所を先に確認してください。

配置した notion-dev-tracker の metadata.version を .notion-dev-tracker.json の skill_version に記録してください。ファイルと設定がすでに一致していれば「更新不要」と伝えてください。設定項目やDBの構成に変更がある場合だけ、更新版のSetup手順に従って既存の接続先で再セットアップしてください。
```

## Codexへの依頼例

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

タスクやIssueはそれぞれ1件のProjectに属します。複数の案件で同じタスクIDを使っている場合は、ProjectとタスクIDを一緒に指定してください。Issueの更新には対象ページのリンクを使えます。

## 既存タスクの取込み

タスクをファイルで管理している場合は、Codexにそのファイルを示し、取込み案を依頼してください。対象、項目の対応、重複・競合、依存関係、件数を確認してから取り込みます。Notionを管理元に切り替える時期と元ファイルの扱いは、取込み結果を確認して決めてください。

## スキルと参照資料

- [通常運用](skills/notion-dev-tracker/SKILL.md) · [セットアップ・再セットアップ](skills/notion-dev-tracker-setup/SKILL.md) · [既存タスクの取込み](skills/notion-dev-tracker-migrate/SKILL.md)
- [DBの項目と状態](skills/notion-dev-tracker/references/schema.md) · [リポジトリの設定例](skills/notion-dev-tracker/references/project-config.example.json)
- [着手条件と状態遷移](skills/notion-dev-tracker/references/workflow.md) · [タスク本文と引き継ぎ](skills/notion-dev-tracker/references/task-template.md) · [Issueの調査とタスク化](skills/notion-dev-tracker/references/issues.md)
