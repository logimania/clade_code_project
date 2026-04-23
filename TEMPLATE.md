# テンプレート利用ガイド

## GitHub Template Repository として使う方法

### Step 1: テンプレートリポジトリとして設定（1回だけ）

GitHubのリポジトリ設定画面で **"Template repository"** にチェックを入れます。

```
Settings → General → Template repository ✅
```

### Step 2: テンプレートから新しいプロジェクトを作成

#### 前提条件

- **Git**
- **Python** 3.12 以上

#### 手順

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
claude     # 開発開始
```

GitHub Actions がバックグラウンドで自動初期化します。ローカルでの操作は不要です。

### 自動初期化で行われること

1. `pyproject.toml` のプロジェクト名と説明を新リポジトリに合わせて更新
2. `CLAUDE.md` の目的欄を更新
3. `src/__init__.py` のプロジェクト名を更新
4. `README.md` を新プロジェクト用にシンプルに再生成
5. テンプレートファイル（`TEMPLATE.md`, `template-setup.yml`）を削除
6. 変更をコミット＆プッシュ

> **ガード条件:** コミット数が1の場合のみ実行されます。テンプレートリポジトリ自体（コミット数2以上）では発火しません。

### Step 3: プロジェクトに合わせてカスタマイズ

| 対象 | やること |
|------|---------|
| `CLAUDE.md` | プロジェクト固有のルール・技術スタックを追記（200行以内） |
| `src/*/CLAUDE.md` | 実際のモジュール構成に合わせて編集 |
| `.claude/settings.json` | ツール許可（`allow`/`deny`）・MCP設定を調整 |
| `.claude/skills/` | 不要なスキル削除、新しいスキル追加（SKILL.md形式） |
| `.claude/settings.local.json` | 個人のトークン・MCP設定を記入 |
| GitHub Secrets | `ANTHROPIC_API_KEY` を設定（Claude Code Action 用、任意） |
