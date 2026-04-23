# CLAUDE.md - リポジトリの記憶（北極星）

> **このファイルは200行以内に収めること。** 詳細ルールは `.claude/rules/` を参照。

## 目的

clade_code_project — Claude Code で構築されたプロジェクト

## リポジトリ構造

```
.
├── CLAUDE.md                     # このファイル（200行以内）
├── .claude/
│   ├── settings.json             # ツール権限、hooks、MCPサーバー
│   ├── skills/                   # 再利用可能なワークフロー（SKILL.md形式）
│   │   ├── code-review/SKILL.md
│   │   ├── refactoring/SKILL.md
│   │   ├── debugging/SKILL.md
│   │   └── release/SKILL.md
│   └── rules/                    # 拡張ルール（CLAUDE.mdからの分割先）
│       ├── context-management.md
│       └── security.md
├── docs/                         # 段階的コンテキスト（参照用、インライン不可）
│   ├── architecture.md           # @docs/architecture.md
│   ├── adr/                      # アーキテクチャ決定記録
│   └── runbooks/                 # 運用手順書
├── .github/
│   ├── ISSUE_TEMPLATE/           # バグ報告・機能要望テンプレート
│   └── pull_request_template.md
├── src/
│   ├── __init__.py               # パッケージ初期化
│   ├── auth/CLAUDE.md            # セキュリティ重要（承認必須）
│   ├── billing/CLAUDE.md         # ビジネス重要（承認必須）
│   └── api/CLAUDE.md             # APIレイヤー規約
├── tests/                        # テストディレクトリ
│   └── test_main.py
├── CLAUDE_PROGRESS.md            # セッション継続（進捗・TODO）
├── CLAUDE_ISSUE.md               # エラー記録（原因・解決策）
└── pyproject.toml                # プロジェクト設定・依存関係
```

## セッション開始時

1. **まず `CLAUDE_PROGRESS.md` を読むこと** — 前回の状態とTODOを確認してから作業開始
2. 現在のタスクに関連する既知の問題がないか `CLAUDE_ISSUE.md` を確認

## 運用ルール

1. **`src/auth/` と `src/billing/` は** ユーザーの明示的な承認なしに変更禁止
2. **コード変更後は必ず** `pytest` を実行
3. **コミット前は必ず** `ruff format` を実行
4. **シークレット、APIキー、認証情報を** コミットしない
5. **各モジュールの** 既存のコードスタイルと命名規則に従う
6. **新機能には必ず** テストを書く
7. **関数は小さく** 1つの責務に集中させる（50行以内）
8. **テストカバレッジは** ビジネスロジックで80%以上を維持（`pytest --cov=src`）
9. **途中で止めない** — タスクを完了するか、`CLAUDE_PROGRESS.md` に残りのTODOを記録してから停止
10. **大きなタスクは** 小さなステップに分割し、ステップごとに `CLAUDE_PROGRESS.md` を更新
11. **依存関係のバージョンを固定** する（例: `"pytest==8.3.4"`、`"pytest>=8.3"` は不可）

セキュリティルールの詳細は @.claude/rules/security.md を参照

## 技術スタック

- 言語: Python 3.12+（型ヒント必須）
- テスト: pytest + pytest-cov
- リンター/フォーマッター: Ruff
- 型チェック: mypy（strictモード）
- Git hooks: pre-commit

## 命名規則

- `snake_case` — 変数・関数・モジュール
- `PascalCase` — クラス
- `UPPER_SNAKE_CASE` — 定数
- `async/await` を優先（コールバックより）
- 相対インポートよりも絶対インポートを優先

## よく使うコマンド

```bash
python -m venv .venv           # 仮想環境作成
source .venv/bin/activate      # 仮想環境の有効化（Windows: .venv\Scripts\activate）
pip install -e ".[dev]"        # 開発用依存関係インストール
pytest                         # テスト実行
pytest --cov=src               # カバレッジ付きテスト実行
ruff check src/ tests/         # リントチェック
ruff format src/ tests/        # コードフォーマット
ruff format --check src/ tests/ # フォーマットチェック
mypy src/                      # 型チェック
pre-commit install             # Git hooks インストール
pre-commit run --all-files     # 全ファイルにpre-commitを実行
```

## コンテキスト管理

- このファイルは毎セッション読み込まれる — 簡潔に保つこと
- 詳細ドキュメントはインラインではなく `@docs/filename.md` で参照
- コンテキスト使用量が多いときは `/compact` で圧縮
- 圧縮時は必ず保持: 変更ファイル一覧、現在のタスク、重要な判断
- コンテキスト管理の詳細は @.claude/rules/context-management.md を参照
