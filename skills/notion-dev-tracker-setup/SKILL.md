---
name: notion-dev-tracker-setup
description: リポジトリにNotion開発タスク管理を導入し、保存先DB・案件・設定・AGENTS.mdを準備するとき、または導入設定を再適用するときに使う。
---
# notion-dev-tracker-setup

## 入力と配布物

対象Gitリポジトリ、利用者のNotion接続、タスクとIssueの保存先を使う。このスキルと隣接する `../notion-dev-tracker/`、`../notion-dev-tracker-migrate/` を同じ配布元コミットから配置する。共通の [DBスキーマ](../notion-dev-tracker/references/schema.md) と [設定例](../notion-dev-tracker/references/project-config.example.json) を読む。

現在の配置先から3ディレクトリを特定し、必要なファイルがすべてそろっているか確認する。配下のreferencesを含めて不足がある場合は、導入プロンプトで固定した配布元コミットから不足分を取得して続ける。

## セットアップ

1. Gitルート、remote、手元の変更、AGENTS.md、既存スキルと設定を確認する。対象remoteをWeb URLに正規化し、末尾の.gitを除く。GitHubのSSH形式とHTTPS形式は同じリポジトリとして扱う。対象remoteが決まらなければ利用者に確認する。実際のタスク一覧・状態・報告や旧運用を持つファイルを調べ、移行候補のパスと根拠を控える。
2. 利用者のNotion接続でページ・DB・データソースを読めることと、必要な作成・更新・リレーション・クエリの機能を確認する。依頼または有効な設定に保存先があれば使う。未指定なら「開発タスクDBをどこに作りますか？ 新しく作る場合は親ページ、既存DBを使う場合はそのDBのリンクを教えてください」と聞いて回答を待つ。アクセス失敗なら接続・共有の修正を案内する。
3. 指定した親ページの子DB、または指定DBに接続されたリレーション先を取得する。作成先の親ページが特定できない場合はそのリンクを聞く。名前が同じという理由で別の場所にあるDBへ接続先を決めない。
4. 実スキーマを同梱のスキーマ定義と照合する。互換性のあるDBは再利用し、空の保存先ではagent-epic、agent-backlog、agent-issueを作る。既存の導入先へのIssue追加・接続もセットアップの対象とし、既存DBを再利用して不足するDB・列・リレーションを補う。スキーマに記載された順序で双方向リレーションを設定する。途中まで成功している場合は実際の状態を確認して未作成部分を続ける。既存の型・選択肢・参照先の変更が必要なら、その差分の同意を得る。
5. 既存の選択肢を意味に対応付け、status_valuesのtask_status・epic_status・approval_status・issue_statusを解決する。新規DBにはスキーマの初期値を使う。既存の日本語等の値がスキーマのキーと一意に対応すればその値を保持し、曖昧な対応は利用者に確認する。既存のtypeの日本語値もそのまま利用する。対象の3DBを再取得して型・状態グループ・選択肢・逆参照を検証する。
6. agent-epicをrepositoryで照合する。保存済みURLも正規化して比較し、一致する1件を再利用する。なければnameをowner/repoなどの識別可能な名前、repositoryを正規化URLとして作成する。複数一致なら候補を示す。
7. `.notion-dev-tracker.json` に実際のrepository、epic_data_source_id・backlog_data_source_id・issue_data_source_id、epic_page_id、status_values、skill_versionを保存する。今回の開発版のskill_versionは `0.1.0-dev`。取得元コミットをsource_revisionに保存する。既存の `.project-tracker.json` がある場合は接続先を検証して引き継ぐ。両設定が食い違う場合は利用者に選択を求める。設定の認証は利用者の接続で行う。
8. 3スキルを対象リポジトリの `.agents/skills/` へ、referencesも含めて完全に配置する。既に配置済みで同じ内容なら再利用する。ローカル編集がある場合は差分を保護して統合する。旧名のnotion-project-tracker、notion-project-tracker-initialise、notion-project-tracker-migrationやproject-tracker系がある場合は、Notion版であることと利用箇所を確認して新名へ引き継ぐ。元のタスク管理の実データは移行案で扱う。
9. AGENTS.mdの次の管理区間を追加・更新する。既存のproject-trackerまたはnotion-project-trackerの区間は、旧Notion版スキルが生成したものと確認できた場合に、案件固有の指示と移行前のタスク管理元の指定を保持して呼び出し名を更新する。作成元が異なる区間は内容を保ち、新しい管理区間を追加する。今回の管理区間を再び更新する場合も、追加された案件固有の指示を保持する。既存のタスク管理元を切り替えるのは、移行スキルで合意した範囲を検証した後とする。実際のepicリンクを記入する。

   <!-- notion-dev-tracker:start -->
   この案件のNotion設定は .notion-dev-tracker.json に記録する。案件の現状・次の作業を案内する際、Notionの開発タスクを運用する際、Issueを登録・調査・タスク化する際は .agents/skills/notion-dev-tracker/SKILL.md を読む。既存タスク管理の移行では .agents/skills/notion-dev-tracker-migrate/SKILL.md に従って移行案を示し、ユーザーの同意後に実行する。既存のタスク管理元を切り替えるのは、合意した移行を検証してからとする。対象epicには特定した案件ページのリンクを記入する。
   <!-- notion-dev-tracker:end -->

10. 配置したnotion-dev-trackerをその場で読み、対象epicと、epicで絞った全タスク・未完了Issueを取得する。取得できた一覧の0件は新規案件として正常。Issueを取得できない場合は未確認と明示する。DBの作成・再利用結果、epicリンク、タスク件数・未完了Issue件数、変更ファイル、取得元コミット、未解決事項を報告する。
11. 実際のタスク管理ファイルの候補があれば、パスと根拠を示してnotion-dev-tracker-migrateの実行を提案する。希望されたら同スキルで調査と具体案の提示へ進む。取り込みは提示した案への同意後に行う。既に移行案を求められている場合はその調査と提案を続行する。

同じ設定で再実行する場合は、既存DB・epic・ファイル・管理区間を照合する。失敗時は保存された範囲を確認し、残りの作業と必要な対応を報告して続ける。
