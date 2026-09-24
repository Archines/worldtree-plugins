# WorldTree の操作手順

ツールは `worldtree_` で始まる名前で示す（ホストによっては前に接頭辞が付く）。現在の同じ接続のスキーマを読み、可用性と必須項目を確認して使う。

## 検索と根拠

- `worldtree_search` は記事・決定の横断検索。スペース区切りは AND、「A OR B」は OR、「A -B」は除外。語の一致で引くため、言い換え（例：「退職金」と「離職手当」）は一致しない。タグでは絞れないので、タグで絞るときは `worldtree_list_articles` の tags を使う。snippet で結論せず、`worldtree_get_article` / `worldtree_get_decision` で必要な本文を読む。
- `worldtree_list_articles` は q・tags・projectId を組み合わせられる（すべて AND）。タグは `worldtree_list_tags` から表記を取得し、推測で綴らない。truncated なら絞り込みや上限調整を行う。
- `worldtree_list_decisions` の q も全文検索（AND・OR・除外）。最大 200 件の上限を全件と取り違えない。
- 記事の検索・一覧には確定済みの記事だけが出る。決定の検索・一覧には draft / decided / superseded / abandoned の決定が status 付きで出る（画面で棄却された AI の記録も abandoned として出る）。未承認の芽はどちらにも出ない。
- 0 件なら別名・短い語・関連案件・タグを試す。不可視・未登録・未承認を区別できなければ断定しない。
- `worldtree_get_neighborhood` は確認済みリンクだけを辿る。depth やリンクの種類を必要最小限にする。truncated を確認し、title が null のノード（閲覧権限がないか表示名が未設定）を欠損として繰り返し取得しない。リンク先の本文が必要なら詳細を読む。
- 現在の方針として扱うのは status が decided の決定だけ。draft・superseded・abandoned の決定を現在の方針と断定しない。矛盾する記録は出典・更新時期・状態を確認する。同じ制度の記事が複数あって内容が食い違うときは、新しい方を黙って選ばず、両方があることと差分を伝える。
- 回答に使った記事は、返された url や citation を出典として添える。記事を使っていないときは出典を書かない。

## タスク・案件

- `worldtree_list_projects` で対象を解決し、`worldtree_list_tasks` で重複・現状を確認する。`worldtree_list_my_tasks` は自分が作成または担当する未完了タスクであり、担当分だけの一覧ではない。
- `worldtree_create_task` は projectId を省略すると既定プロジェクト「一般」に入る。適切な案件があれば紐付けるが、機密性や文脈が曖昧な場合に安易に一般へ入れない。
- 担当者は `worldtree_search_users` / `worldtree_list_users` で本人を確実に解決し、`worldtree_assign_task` を使う。曖昧な名前は確認する。`worldtree_bulk_assign_tasks` は最大 100 件で、各件の結果を確認する。
- `worldtree_get_task` の最新の lockVersion で `worldtree_update_task` / `worldtree_update_task_status` を実行する。期限の相対表現は利用者のタイムゾーンに合わせる。時刻・担当・優先度を創作しない。任意項目は省略してサーバーの既定値を用い、結果を確認する。
- parentTaskId に指定できるのは、親を持たない最上位タスクだけ。
- `worldtree_create_project` / `worldtree_update_project` は依頼された範囲だけ行う。案件リードは参加メンバーであることを確認できなければ割り当てない。Slack からの依頼でチャンネル ID が分かる場合は `worldtree_get_project_by_slack_channel` を使い、見つからない理由を断定しない。
- タスクへのコメント、担当解除、削除も、対象を確認し依頼があるときだけ行う。削除は admin・owner だけが使え、元に戻せない。不要になっただけなら status を cancelled にする方法も示す。

## 記事・公開範囲

- `worldtree_create_article` は、提供された原文を要約で置き換えずに確定済みの記事として保存する（承認待ちにはならず、すぐ検索に出る）。10 万文字を超える原文は分割して保存する。要約を頼まれたときは、要約と原文を区別する。
- projectIds は閲覧範囲。`worldtree_list_projects` で選び、既定プロジェクト「一般」は組織の全員が読めることを理解して選ぶ。タグは検索の目印であり、アクセス制御ではない。
- `worldtree_update_article` は本文の全文置換。編集できるのは記事の作成者と admin・owner。`worldtree_get_article` で現状と lockVersion を取得し、変更対象以外を保った全文を送る。10 万文字の上限は更新後の本文全体にかかる。競合したら読み直し、意味の衝突があれば確認する。
- `worldtree_set_article_projects` / `worldtree_set_article_tags` は全置換。追加するときも現状の全量を取得して保持する。公開範囲を広げる根拠がない場合は確認する。
- `worldtree_get_article` の projects には、接続中の利用者が見られないプロジェクトへの紐付けが出ない。`worldtree_set_article_projects` で置き換えるとその紐付けも外れ、閲覧範囲が知らないうちに狭まることがある。見えない紐付けがありうるときは、実行前にそれを伝える。存在しないか見られないプロジェクトの ID は紐付けられず、残りの ID で置き換えが行われる。複数の記事をまとめて処理するときは最大 50 件で、途中で失敗するとそれまでの分は適用済みになるので、結果を件ごとに確認する。
- `worldtree_list_unlinked_articles` は、作成者と admin・owner にしか見えない未紐付けの記事を整理するためのもの。offset で続きも確認し、一括で全社公開しない。
- 記事へのコメント・いいね・取消・削除は、ユーザーの依頼があるときだけ行う。

## 決定・ナレッジの芽

- 記録を頼まれたとき、または了承を得たときに、対象を分類する。議事録は記事、再利用したい知識は `worldtree_create_knowledge_candidate`、会話で決まったことは `worldtree_record_decision`。決定に必要な理由や却下案を創作しない。
- `worldtree_record_decision` の記録は承認待ちの芽で、検索にはまだ出ない。会話で「承認」と言われても、芽を `worldtree_create_decision` で複製して承認を迂回しない。芽は更新・削除できない（画面で確定・棄却する）。
- 確定済みの決定として新しく残すよう明示的に頼まれたときは、`worldtree_create_decision` を使う（承認待ちにはならず、すぐ検索に出る）。業務上の決定の状態（draft / decided / abandoned）と、人が確認したかどうかを混同しない。
- `worldtree_update_decision` の options は全置換。
- `worldtree_create_link` は依頼があるときだけ使う。作ったリンクはすぐ確定済み（confirmed）になる。種類ごとの向きと対象を確認し、AI の推測を人が確認したリンクとして登録しない。supersedes を作ると、対象の決定は superseded（上書き済み）になる。
- `worldtree_confirm_link` / `worldtree_reject_link` は、利用者が対象のリンクを特定して頼んだときだけ使う。
- `worldtree_delete_decision` は admin・owner の接続でだけ使え、元に戻せない。誤って作ったものの掃除を頼まれたときだけ、確認してから使う。通常の廃止は status を abandoned にする。
- 複数を保存するときは、依頼された原文記事 → 了承を得た決定・知識の芽 → 依頼されたタスク → 関係するリンクの順を基本とし、各結果を保持する。使えない項目を発明しない。
