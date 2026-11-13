# Notion Dev Tracker

Codexと一緒に、開発タスクとIssueをNotionで管理するためのスキルです。リポジトリごとにProjectを選び、作業の計画から実装、検証、引き継ぎまでをタスクに記録します。

## 導入手順

1. 導入先のリポジトリをCodexで開き、Notionを接続します。Codexにはリポジトリの編集権限、Notionの接続には保存先の閲覧・編集権限が必要です。
2. 次のプロンプトをコピーしてCodexに渡します。
3. 保存先を聞かれたら、新しいDBを作る親ページ、または既存のDBのリンクを渡します。
4. 返ってきたProjectリンクを開き、対象の案件と、作成または再利用されたDBを確認します。

```markdown
このリポジトリにNotion Dev Trackerをセットアップしてください。

配布元は https://github.com/umecchi1098/notion-dev-tracker です。公開mainの先端コミットSHAを一度取得し、そのSHAから次の3ディレクトリを配下のファイルごと取得してください。

- skills/notion-dev-tracker/
- skills/notion-dev-tracker-setup/
- skills/notion-dev-tracker-migrate/

導入先のGitルート、既存の指示、設定、タスクの管理元、手元の変更を確認してください。3スキルともバージョンが0.2.0-devであることを確認し、.agents/skills/ に同名で配置してください。各SKILL.mdの先頭のYAML frontmatterを含め、全ファイルを取得元の内容と照合してください。同じ内容は再利用し、ローカル編集がある場合はそのファイルを保持して差分を示してください。版や取得元を確認できない場合や差分が残る場合は、そこで止めてください。設定のsource_revisionには実際に取得した配布元のSHAを記録してください。

配置した .agents/skills/notion-dev-tracker-setup/SKILL.md をこのセッションで読み、私のNotion接続で実行してください。保存先の指定が必要なら、新しいDBを作る親ページか既存DBのリンクを聞いてください。書込み検証を行うのは、私が専用のテスト保存先を指定した場合だけです。

セットアップ後はnotion-dev-trackerを読み、このProjectの現状を確認してください。Projectリンク、作成・再利用したDB、配置ファイル、バージョン、取得元SHA、取得範囲と件数を示してください。確認できなかった項目があれば、その内容も教えてください。今回の依頼はセットアップと現状確認までです。
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
