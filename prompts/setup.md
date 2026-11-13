# 導入プロンプト

対象リポジトリをCodexで開き、次のコードブロックをコピーして渡してください。

```markdown
このリポジトリにNotion Dev Trackerをセットアップしてください。

配布原本は次の公開リポジトリです。
https://github.com/umecchi1098/notion-dev-tracker

まず配布元のmainの先端コミットSHAを解決し、今回の取得元をそのコミットに固定してください。以後はすべて同じコミットから取得し、取得元SHAをセットアップ結果に記録してください。

対象リポジトリのGitルートと既存の指示・手元の変更を確認し、配布元にある次の3つのディレクトリを、referencesなどの配下ファイルも含めて完全に取得してください。

- skills/notion-dev-tracker/
- skills/notion-dev-tracker-setup/
- skills/notion-dev-tracker-migrate/

それぞれを対象リポジトリの.agents/skills/配下へ同じディレクトリ名で配置してください。SKILL.mdのYAML frontmatterを先頭に保持してください。既存の同一内容は再利用し、ローカルで編集された内容があれば差分を確認して引き継いでください。

配置した.agents/skills/notion-dev-tracker-setup/SKILL.mdを今のセッションで読み、直ちに実行してください。Notionへの操作には利用者の接続を使ってください。保存先が未指定なら、新規DBを作る親ページか既存DBのリンクを私に聞いてください。DBの作成と再利用は、同梱のスキーマと実際の保存先を照合して進めてください。

セットアップ後はnotion-dev-trackerを起動し、対象案件のリンク、タスク件数、配置ファイル、取得元コミットSHA、未解決事項を報告してください。

既存のタスク管理ファイルが見つかった場合は、notion-dev-tracker-migrateの実行を提案してください。同スキルで現在の管理方法を調べ、Notionへの具体的な移行案を示し、私がその案に同意してから移行してください。正本の切替も移行案に含めてください。
```
