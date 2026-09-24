# WorldTree plugins for Claude

Claude plugins for [WorldTree](https://worldtree.archines.co.jp/connectors/worldtree), the company knowledge OS by Archines Inc.
The `worldtree` plugin connects Claude to your WorldTree organization (remote MCP server with OAuth) and adds a skill that lets Claude answer with your company's own knowledge, meeting notes, tasks, and decisions.
Claude writes to WorldTree only when you ask or agree. Decisions, knowledge, and skill candidates that Claude proposes are saved as pending sprouts that a person approves in WorldTree; articles, tasks, and confirmed decisions you explicitly ask for are saved right away.

- Documentation: https://worldtree.archines.co.jp/connectors/worldtree
- Privacy policy: https://worldtree.archines.co.jp/privacy
- Support: https://worldtree.archines.co.jp/contact

---

## WorldTree とは

WorldTree は、社内のナレッジ記事・議事録・タスク・意思決定・行動指針（バリュー）を 1 か所に集め、AI と人が一緒に育てていく企業 OS です。
このリポジトリは、Claude（Claude Code・Cowork）から WorldTree を使うためのプラグインを配布するマーケットプレイスです。

## 収録しているプラグイン

| プラグイン                         | 内容                                                                                   |
| ---------------------------------- | -------------------------------------------------------------------------------------- |
| [`worldtree`](worldtree/README.md) | WorldTree の MCP サーバーへの接続（OAuth）と、スキル「WorldTree 共通業務アシスタント」 |

## インストール（Claude Code）

```text
/plugin marketplace add Archines/worldtree-plugins
/plugin install worldtree@worldtree-plugins
```

インストール後、`/mcp` を開いて `plugin:worldtree:worldtree` を選び、サインインします。ブラウザで WorldTree のログイン画面が開くので、WorldTree のアカウントでログインし、接続する組織を選んでください。

## 必要なもの

- WorldTree のアカウントと、WorldTree を利用中の組織への所属
- Claude Code（または Cowork）

## 使い方の例

- 「うちの経費精算のルールを教えて」— 社内の記事を検索し、出典の記事へのリンクを添えて答えます。
- 「今週が期限の自分のタスクを一覧にして」— 自分が作成または担当している未完了タスクを期限順にまとめます。
- 「先週の定例の議事録をもとに、次の打ち合わせの準備をして」— 議事録・関連タスク・過去の決定を横断して準備します。
- 「いま決まったことを、承認待ちの意思決定として WorldTree に残して」— 承認待ちの「芽」として記録し、WorldTree の画面で人が確定するまで検索には出ません。

詳しくは [worldtree/README.md](worldtree/README.md) を参照してください。

## プライバシーとサポート

- プライバシーポリシー: https://worldtree.archines.co.jp/privacy
- 利用者向けドキュメント: https://worldtree.archines.co.jp/connectors/worldtree
- お問い合わせ: https://worldtree.archines.co.jp/contact

## ライセンス

このリポジトリのコードと文書は [MIT](LICENSE) ライセンスで提供します。ただし、WorldTree の名称とロゴ（`worldtree/skills/worldtree-company-os/assets/icon.svg`）は Archines Inc. に帰属し、MIT ライセンスの対象外です。

The code and documentation in this repository are licensed under [MIT](LICENSE). The WorldTree name and logo belong to Archines Inc. and are not licensed under MIT.
