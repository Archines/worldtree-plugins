# WorldTree for AI assistants

Connect your AI assistant to [WorldTree](https://worldtree.archines.co.jp/connectors/worldtree), the company knowledge OS by Archines Inc.
This repository provides two open-format building blocks that work with most AI assistants, including Claude, ChatGPT, and Codex:

- **A remote MCP server** (Streamable HTTP, OAuth 2.1) for your company's knowledge, meeting notes, tasks, and decisions in WorldTree.
- **An Agent Skill** (`SKILL.md` with reference files) that lets the assistant answer with your company's own context.

The assistant writes to WorldTree only when you ask or agree. Decisions, knowledge, and skill candidates it proposes are saved as pending sprouts that a person approves in WorldTree; articles, tasks, and confirmed decisions you explicitly ask for are saved right away.
For Claude Code, this repository is also a plugin marketplace that installs both at once.

- Documentation: https://worldtree.archines.co.jp/connectors/worldtree
- Privacy policy: https://worldtree.archines.co.jp/privacy
- Support: https://worldtree.archines.co.jp/contact

---

## WorldTree とは

WorldTree は、社内のナレッジ記事・議事録・タスク・意思決定・行動指針（バリュー）を 1 か所に集め、AI と人が一緒に育てていく企業 OS です。
このリポジトリは、生成 AI から WorldTree を使うための部品（MCP サーバーへの接続とスキル）を配布しています。どちらも特定の AI に依存しない公開された形式なので、Claude・ChatGPT・Codex など、多くの生成 AI で使えます。

## 収録しているもの

| 部品                                                                                                     | 形式                                           | 使える AI                                            |
| -------------------------------------------------------------------------------------------------------- | ---------------------------------------------- | ---------------------------------------------------- |
| MCP サーバー `https://worldtree.archines.co.jp/api/mcp`                                                  | リモート MCP（Streamable HTTP・OAuth 2.1）     | リモート MCP に対応した AI（Claude・ChatGPT など）   |
| スキル [`worldtree-company-os`](worldtree/skills/worldtree-company-os)（WorldTree 共通業務アシスタント） | Agent Skills 形式（`SKILL.md` と付属ファイル） | スキルを取り込める AI（Claude・ChatGPT・Codex など） |

スキルは、WorldTree の MCP サーバーにつないだ状態で使います。スキルを入れただけでは WorldTree にはつながりません。詳しい中身は [worldtree/README.md](worldtree/README.md) を参照してください。

## AI ごとの入れ方

どの AI でも、次の 2 つを行います。

1. **WorldTree に接続する**：MCP サーバーの URL `https://worldtree.archines.co.jp/api/mcp` を AI に追加し、OAuth でサインインします。WorldTree の同意画面で、接続する組織を選んでください。1 つの接続は 1 つの組織に固定されます。
2. **スキルを入れる**：[`worldtree/skills/worldtree-company-os`](worldtree/skills/worldtree-company-os) フォルダを、AI に取り込みます。zip で入れる AI では、このフォルダごと zip にしてください（フォルダ名は変えない）。

### Claude Code

プラグインとして入れると、接続とスキルが一度に入ります。

```text
/plugin marketplace add Archines/worldtree-plugins
/plugin install worldtree@worldtree-plugins
```

`Archines/worldtree-plugins` の形は、既定で SSH を使って GitHub から取得します。GitHub に SSH 鍵を登録していない場合は、1 行目を HTTPS の URL に置き換えてください。

```text
/plugin marketplace add https://github.com/Archines/worldtree-plugins.git
```

インストール後、`/mcp` を開いて `plugin:worldtree:worldtree` を選び、サインインします。

claude.ai のコネクタで WorldTree をすでに追加している場合、Claude Code は同じ接続先のプラグイン側の接続を使わず、claude.ai のコネクタの接続を使います。このとき接続先の組織は、コネクタで選んだものになります。スキルはどちらの接続でも同じように動きます。

### Claude（claude.ai・デスクトップアプリ）

1. **接続**：「カスタマイズ › コネクタ」から、MCP サーバーの URL をカスタムコネクタとして追加します。
2. **スキル**：「カスタマイズ › スキル」の「＋」→「スキルを作成」→「スキルをアップロード」から、`worldtree-company-os` フォルダの zip を入れます。スキルを使うには、コード実行が有効になっている必要があります。

### ChatGPT

1. **接続**：`https://chatgpt.com/plugins` を開きます。
   - ワークスペースで WorldTree が共有されていれば、一覧から開いて「接続」を押します。
   - 一覧に無ければ、設定で開発者モードをオンにしてから、右上の「＋」で URL を追加し、認証方法に「OAuth」を選びます。会社で契約している場合、開発者モードは管理者しか変更できないことがあります。
2. **スキル**：`https://chatgpt.com/skills` の「＋」→「パソコンからアップロード」から、`worldtree-company-os` フォルダの zip を入れます。

### Codex など、フォルダからスキルを読む AI

1. **接続**：リモート MCP に対応していれば、MCP サーバーの URL を追加します。追加の方法は、それぞれの AI の説明を参照してください。
2. **スキル**：`worldtree-company-os` フォルダを、その AI がスキルを読むフォルダにコピーします（例：Codex は `~/.codex/skills/`、Claude Code は `~/.claude/skills/`）。

`agents/openai.yaml` は、ChatGPT や Codex で使うときの表示名とアイコンの設定です。ほかの AI では使われません。

## 使い方の例

- 「うちの経費精算のルールを教えて」— 社内の記事を検索し、出典の記事へのリンクを添えて答えます。
- 「今週が期限の自分のタスクを一覧にして」— 自分が作成または担当している未完了タスクを期限順にまとめます。
- 「先週の定例の議事録をもとに、次の打ち合わせの準備をして」— 議事録・関連タスク・過去の決定を横断して準備します。
- 「いま決まったことを、承認待ちの意思決定として WorldTree に残して」— 承認待ちの「芽」として記録し、WorldTree の画面で人が確定するまで検索には出ません。

## 必要なもの

- WorldTree のアカウントと、WorldTree を利用中の組織への所属
- リモート MCP とスキルに対応した AI

## プライバシーとサポート

- プライバシーポリシー: https://worldtree.archines.co.jp/privacy
- 利用者向けドキュメント: https://worldtree.archines.co.jp/connectors/worldtree
- お問い合わせ: https://worldtree.archines.co.jp/contact

## ライセンス

このリポジトリのコードと文書は [MIT](LICENSE) ライセンスで提供します。ただし、WorldTree の名称とロゴ（`worldtree/skills/worldtree-company-os/assets/icon.svg`）は Archines Inc. に帰属し、MIT ライセンスの対象外です。

The code and documentation in this repository are licensed under [MIT](LICENSE). The WorldTree name and logo belong to Archines Inc. and are not licensed under MIT.
