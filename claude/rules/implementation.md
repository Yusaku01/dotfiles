# 実装ガイド

## DocbaseCLIツール

### 基本コマンド

```bash
# 投稿
node docbase-cli.js create ./post/xxx.md

# 更新
node docbase-cli.js update ./post/xxx.md [Docbase投稿ID省略可]

# 削除
node docbase-cli.js delete <Docbase投稿ID or タイトル>

# 投稿ID取得
node docbase-cli.js getid ./post/xxx.md
```

### 主な機能

- Markdownファイルをファイル名をタイトルとしてDocbaseに投稿
- frontmatterのdocbase_idまたはタイトル検索による記事更新
- タイトルまたはIDによる記事削除
- 記事IDの取得
- 投稿時にfrontmatterのdocbase_idが自動更新される
- 投稿スコープはデフォルトで'private'に設定される

## 開発時の注意事項

- プロジェクトはES Modules使用（type: "module"）
- 日本語記事が中心のため、ファイル名やコメントは日本語対応
- 記事のfrontmatterを変更する際は、docbase_idフィールドを維持する
- 新しい記事カテゴリを作成する場合は、適切なディレクトリ構造を維持する
- このプロジェクトにはビルド、テスト、リンティングプロセスは設定されていません（単純なCLIツール + 知識管理システムのため）

## 使用例

### 新規記事の投稿
```bash
# 記事を作成してDocbaseに投稿
node docbase-cli.js create ./post/新しい記事.md
```

### 既存記事の更新
```bash
# frontmatterにdocbase_idがある場合は自動検索
node docbase-cli.js update ./post/既存記事.md

# IDを直接指定する場合
node docbase-cli.js update ./post/既存記事.md 12345
```

### 記事の削除
```bash
# IDで削除
node docbase-cli.js delete 12345

# タイトルで削除
node docbase-cli.js delete "記事のタイトル"
```
