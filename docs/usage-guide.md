# 利用手順書

## 概要

このテンプレートは、Claude Code を活用した Python プロジェクトの構造化されたスタートポイントを提供します。
「Use this template」で新規リポジトリを作成すると、GitHub Actions が自動で初期化を行います。

---

## 1. 新規プロジェクトの作成

### 1.1 前提条件

| 項目 | 要件 |
|------|------|
| Python | 3.12 以上 |
| Git | 最新版推奨 |
| Claude Code | CLI インストール済み |

### 1.2 手順

#### Step 1: テンプレートからリポジトリを作成

1. [テンプレートリポジトリ](https://github.com/logimania/claude_project_template) を開く
2. 緑色の **"Use this template"** ボタンをクリック
3. **"Create a new repository"** を選択
4. 以下を入力:
   - **Repository name**: プロジェクト名（例: `my-awesome-api`）
   - **Description**: プロジェクトの説明（例: `ユーザー管理APIサーバー`）
   - **Public / Private**: 任意
5. **"Create repository"** をクリック

#### Step 2: 自動初期化を待つ（約1分）

GitHub Actions が自動で以下を実行します:
- `pyproject.toml` のプロジェクト名・説明を更新
- `CLAUDE.md` の目的欄を更新
- `src/__init__.py` のプロジェクト名を更新
- `README.md` を新プロジェクト用に再生成
- テンプレートファイル（`TEMPLATE.md`, `template-setup.yml`）を削除
- 変更をコミット＆プッシュ

> Actions タブで「テンプレート初期化」ワークフローの完了を確認できます。

#### Step 3: クローンしてセットアップ

```bash
# クローン
git clone https://github.com/<your-name>/my-awesome-api.git
cd my-awesome-api

# 仮想環境を作成・有効化
python -m venv .venv
source .venv/bin/activate        # macOS / Linux
# .venv\Scripts\activate         # Windows（コマンドプロンプト）
# .venv\Scripts\Activate.ps1     # Windows（PowerShell）

# 開発用依存関係をインストール
pip install -e ".[dev]"

# Git hooks をインストール
pre-commit install
```

#### Step 4: Claude Code で開発開始

```bash
claude
```

Claude Code が `CLAUDE.md` を自動で読み込み、プロジェクトのルールと構造を理解した状態で開発を支援します。

---

## 2. 日常の開発ワークフロー

### 2.1 コーディング

```bash
# Claude Code を起動して開発
claude

# 開発中のテスト実行
pytest

# カバレッジ確認
pytest --cov=src --cov-report=term-missing
```

### 2.2 コミット前チェック

```bash
# リント（自動修正あり）
ruff check --fix src/ tests/

# フォーマット
ruff format src/ tests/

# 型チェック
mypy src/

# テスト
pytest
```

> `pre-commit install` 済みであれば、`git commit` 時に ruff が自動実行されます。

### 2.3 Claude Code のスキル活用

```bash
# Claude Code 内で使えるスラッシュコマンド
/code-review src/           # コードレビュー
/debugging エラーの説明      # バグ調査
/refactoring src/module.py  # リファクタリング
/release patch              # リリース準備
```

---

## 3. プロジェクトのカスタマイズ

### 3.1 CLAUDE.md の編集

プロジェクト固有のルールを追記します。**200行以内**に収めてください。

```markdown
## 追加ルール
- FastAPI を使用する
- データベースは PostgreSQL（asyncpg 経由）
- 全エンドポイントに認証ミドルウェアを適用
```

200行を超えそうな場合は `.claude/rules/` にファイルを分割し、`@.claude/rules/filename.md` で参照します。

### 3.2 モジュール構成の変更

不要なモジュールを削除し、必要なモジュールを追加します:

```bash
# 不要なモジュールを削除
rm -rf src/billing/

# 新しいモジュールを追加
mkdir -p src/notifications/
```

新モジュールに `CLAUDE.md` を配置すると、Claude Code がモジュール固有のルールを理解します:

```bash
cat > src/notifications/CLAUDE.md << 'EOF'
# 通知モジュール — ローカルコンテキスト
## 責務
- Email / Push / SMS 通知の送信
## 主要ルール
1. 送信レートリミットを必ず設定する
2. テンプレートエンジンでHTMLインジェクションを防ぐ
EOF
```

### 3.3 依存関係の追加

`pyproject.toml` に追加します。**バージョンは固定**してください:

```toml
[project]
dependencies = [
    "fastapi==0.115.6",
    "uvicorn==0.34.0",
    "sqlalchemy==2.0.36",
]
```

追加後:

```bash
pip install -e ".[dev]"
```

### 3.4 スキルの追加

`.claude/skills/` に SKILL.md を作成します:

```bash
mkdir -p .claude/skills/api-design
cat > .claude/skills/api-design/SKILL.md << 'EOF'
---
name: api-design
description: API設計レビュー
disable-model-invocation: true
argument-hint: [エンドポイントパス]
allowed-tools: Read, Grep, Glob
---

$ARGUMENTS のAPI設計をレビューしてください。
EOF
```

### 3.5 ツール権限の調整

`.claude/settings.json` の `permissions` を編集します:

```json
{
  "permissions": {
    "allow": [
      "Bash(uvicorn *)",
      "Bash(alembic *)"
    ]
  }
}
```

### 3.6 Claude Code Action の有効化（任意）

GitHub の Issue や PR で `@claude` とメンションすると Claude が自動対応します。

1. GitHub リポジトリの **Settings → Secrets and variables → Actions**
2. **New repository secret** で `ANTHROPIC_API_KEY` を追加
3. `.github/workflows/claude.yml` が自動で動作開始

---

## 4. CI / CD

### 4.1 自動チェック内容

push / PR 時に以下が自動実行されます:

| チェック | コマンド | 説明 |
|----------|---------|------|
| リント | `ruff check src/ tests/` | コード品質チェック |
| フォーマット | `ruff format --check src/ tests/` | コードスタイル確認 |
| 型チェック | `mypy src/` | 静的型解析 |
| テスト | `pytest --cov=src` | テスト実行 + カバレッジ |

### 4.2 CI が失敗したら

```bash
# ローカルで同じチェックを実行
ruff check src/ tests/           # リントエラー確認
ruff format --check src/ tests/  # フォーマットエラー確認
mypy src/                        # 型エラー確認
pytest --cov=src                 # テスト失敗確認
```

---

## 5. セッション管理

### 5.1 作業の中断と再開

Claude Code は `CLAUDE_PROGRESS.md` を使ってセッション間の継続性を保ちます。

- **中断時**: Claude Code が自動で進捗と残りの TODO を `CLAUDE_PROGRESS.md` に記録
- **再開時**: Claude Code が `CLAUDE_PROGRESS.md` を読み込んで前回の状態を復元

### 5.2 エラーの記録

解決したバグは `CLAUDE_ISSUE.md` に記録されます。同じ問題の再発を防ぎます。

---

## 6. トラブルシューティング

### テンプレート初期化が実行されない

- **原因**: GitHub Actions が無効、またはリポジトリの Actions 権限が不足
- **確認**: リポジトリの **Settings → Actions → General** で「Allow all actions」が有効か確認
- **対処**: Actions タブで「テンプレート初期化」ワークフローを手動実行（Re-run）

### pre-commit が動かない

```bash
# pre-commit を再インストール
pip install pre-commit
pre-commit install
pre-commit run --all-files  # 全ファイルで動作確認
```

### mypy でエラーが出る

```bash
# 型スタブが不足している場合
pip install types-requests  # 例: requests の型スタブ
mypy src/
```

### 仮想環境が見つからない

```bash
# 仮想環境を再作成
python -m venv .venv
source .venv/bin/activate
pip install -e ".[dev]"
```
