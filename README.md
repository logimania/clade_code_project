# Claude Code Structured Project Template

Claude Codeを「プロジェクト専属エンジニア」として最大限活用するためのリポジトリテンプレートです。

> **"Prompting is temporary. Structure is permanent."**
> — プロンプトの質よりも、リポジトリの構造がClaude Codeの出力品質を決める。

---

## 前提条件

- **Python** 3.12 以上
- **Git**

---

## Quick Start

1. GitHub でこのテンプレートリポジトリを開く
2. **"Use this template"** → **"Create a new repository"** をクリック
3. リポジトリ名と説明（Description）を入力して作成
4. **1分ほど待ってから**クローンして開発開始:

```bash
git clone https://github.com/<your-name>/my-project.git my-project
cd my-project
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -e ".[dev]"
claude
```

> GitHub Actions が自動で初期化します（プロジェクト名の置換・テンプレートファイル削除）。
> 手動セットアップは不要です。
>
> 詳しいテンプレート利用方法は [TEMPLATE.md](./TEMPLATE.md) を参照してください。

---

## プロジェクト構造

```
.
├── CLAUDE.md                         # Repo Memory（200行以内の北極星）
├── .claude/
│   ├── settings.json                 # Tools & Permissions
│   ├── skills/                       # Expert Modes（SKILL.md形式）
│   │   ├── code-review/SKILL.md
│   │   ├── refactoring/SKILL.md
│   │   ├── debugging/SKILL.md
│   │   └── release/SKILL.md
│   ├── rules/                        # Extended Rules（CLAUDE.mdの分割先）
│   │   ├── context-management.md
│   │   └── security.md
│   └── hooks/
│       ├── check-claude-md-size.sh   # CLAUDE.md行数チェック
│       └── README.md                 # Hook設定ガイド
├── .pre-commit-config.yaml           # Git hooks（ruff自動実行）
├── docs/                             # Progressive Context
│   ├── architecture.md
│   ├── adr/
│   └── runbooks/
├── .claude/
│   └── settings.local.json.example   # 個人設定のテンプレート
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   ├── pull_request_template.md
│   └── workflows/
│       ├── ci.yml                    # CI（テスト・リント自動実行）
│       ├── claude.yml                # Claude Code PR/Issue自動対応
│       └── template-setup.yml        # テンプレート初期化（自動実行）
├── src/
│   ├── __init__.py                   # パッケージ初期化
│   ├── auth/CLAUDE.md                # Local Context（セキュリティ重要）
│   ├── billing/CLAUDE.md             # Local Context（ビジネス重要）
│   └── api/CLAUDE.md                 # Local Context（API規約）
├── tests/
│   └── test_main.py                  # テスト
├── CLAUDE_PROGRESS.md                # セッション継続（進捗・TODO）
├── CLAUDE_ISSUE.md                   # エラー記録（原因・解決策の蓄積）
├── .gitignore
└── pyproject.toml                    # プロジェクト設定・依存関係
```

---

## 構成要素

### 1. CLAUDE.md — Repo Memory（リポジトリの記憶）

プロジェクトのルートに置く「北極星」ファイル。Claude Codeは起動時に自動で読み込みます。

**重要：200行以内に収める。** 200行を超えると切り捨てられ、Claudeが重要なルールを見逃します。

**記載すべき内容：**
- プロジェクトの目的（1〜2行）
- ディレクトリ構造の概要
- 技術スタック、コーディング規約
- 絶対に守るべきルール
- よく使うコマンド

**記載すべきでない内容：**
- 詳細なAPIドキュメント（`@docs/` 配下に別ファイルとして配置し参照）
- フレームワークのチュートリアル（自明な知識）
- スプリント目標やTODO（`CLAUDE_PROGRESS.md` に記載）

```markdown
# CLAUDE.md
## Purpose
ユーザー認証・課金管理を行うバックエンドAPI

## Tech Stack
Python 3.12+ / pytest / Ruff / mypy

## Rules
1. src/auth/ と src/billing/ は承認なしに変更禁止
2. コミット前にテストとリンターを必ず実行

For security rules, see @.claude/rules/security.md
```

---

### 2. .claude/rules/ — Extended Rules（拡張ルール）

CLAUDE.md が大きくなりすぎた場合の分割先です。トピック別にファイルを作成します。

```
.claude/rules/
├── context-management.md    # コンテキストウィンドウ管理ルール
└── security.md              # セキュリティルール
```

CLAUDE.md から `@.claude/rules/security.md` のように参照できます。

---

### 3. .claude/skills/ — Expert Modes（エキスパートモード）

再利用可能なワークフローテンプレート。**SKILL.md 形式**（ディレクトリ + フロントマター）で作成します。

```
.claude/skills/
├── code-review/SKILL.md     # /code-review で呼び出し
├── refactoring/SKILL.md     # /refactoring で呼び出し
├── debugging/SKILL.md       # /debugging で呼び出し
└── release/SKILL.md         # /release で呼び出し
```

**使い方：**

```bash
/code-review src/api/      # コードレビュー
/debugging ログインできない  # バグ調査
/refactoring src/api/routes.py  # リファクタリング
/release patch              # リリース準備
```

---

### 4. .claude/hooks/ — Guardrails（自動ガードレール）

Claude Codeの操作に対して自動チェックを走らせる仕組み。`.claude/settings.json` で設定します。

> 詳しい設定方法は [.claude/hooks/README.md](./.claude/hooks/README.md) を参照。

---

### 5. .claude/settings.json — Tools & Permissions（ツールと権限）

Claude Codeのツール許可・拒否、フック、MCP連携を一元管理します。

```json
{
  "permissions": {
    "allow": [
      "Read", "Glob", "Grep",
      "Bash(pytest *)",
      "Bash(ruff check *)",
      "Bash(ruff format *)",
      "Bash(mypy *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force)",
      "Bash(git reset --hard)",
      "Read(.env)"
    ]
  }
}
```

---

### 6. docs/ — Progressive Context（段階的コンテキスト）

ドキュメントをプロンプトに詰め込まず、参照可能な場所に配置します。

```
docs/
├── architecture.md              # システムアーキテクチャ図
├── adr/
│   └── 001-modular-architecture.md  # なぜこの設計にしたか
└── runbooks/
    └── incident-response.md     # インシデント対応フロー
```

---

### 7. Local CLAUDE.md — モジュール固有のコンテキスト

重要モジュールのディレクトリに配置する専用の指示書。

```
src/
├── auth/CLAUDE.md      # 「パスワードは必ずbcryptで12ラウンド以上」
├── billing/CLAUDE.md   # 「金額計算にfloatを使わない、冪等性キー必須」
└── api/CLAUDE.md       # 「エラーレスポンス形式を統一」
```

---

### 8. CLAUDE_PROGRESS.md — セッション継続性

セッションをまたぐ作業の継続性を保証するファイルです。

---

### 9. CLAUDE_ISSUE.md — エラー学習サイクル

バグの原因と解決策を蓄積し、同じ間違いを繰り返さないためのファイルです。

---

### 10. Git Hooks — pre-commit

`pre-commit` で、コミット時に Ruff のリント・フォーマットを自動実行します。

```bash
pip install pre-commit
pre-commit install
```

---

## コンテキストウィンドウ管理

| 原因 | 対策 |
|------|------|
| CLAUDE.md が長すぎる | 200行以内に収める。超えたら `.claude/rules/` に分割 |
| ドキュメントを直接貼り付け | `@docs/filename.md` で参照に切り替え |
| 大量のファイル探索 | Explore サブエージェントに委任 |
| 長い会話の蓄積 | `/compact` でコンテキスト圧縮、`/clear` でリセット |

---

## License

MIT
