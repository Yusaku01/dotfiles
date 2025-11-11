# プロジェクト概要

このリポジトリは、Obsidianの個人知識管理システムおよびブログ記事管理システムです。主な機能として、DocbaseへのCLI投稿ツールとマークダウンベースの記事管理を提供します。

## プロジェクト構造

```
saku-space/
├── docbase-cli.js           # DocbaseAPI連携用CLIツール
├── package.json             # プロジェクト設定
├── note/                   # カテゴリ別記事整理
│   ├── about-backend/      # バックエンド関連記事
│   ├── about-design/       # デザイン関連記事
│   ├── about-frontend/     # フロントエンド関連記事
│   ├── other/             # その他の記事
│   ├── simple-note/       # 簡単なメモ
│   └── articles-reference/ # 記事リンク参照
├── work/                  # 仕事用ディレクトリ
│   ├── note/             # 作業メモ
│   └── projects/         # 個別プロジェクト管理
├── post/                  # Docbase投稿用記事
├── articles/              # zenn投稿用記事
├── books/                 # 書籍関連ノート
├── slide-projects/        # プレゼンテーションプロジェクト
│   ├── claude-code-lt-slides/ # Claude Codeプレゼン資料
│   └── figma-dev-mode-mcp/    # Figma開発モードMCP資料
├── img/                   # 画像ファイル
└── Excalidraw/            # 図表ファイル
```

## ファイル管理

- `./note/`配下の記事は、個人用メモの置き場です
- `./articles/`配下はzenn投稿用記事の置き場です
- `./post/`配下はDocbase投稿用記事の置き場です
- `./work/`配下は仕事用メモの置き場です
- 記事のfrontmatterに`docbase_id`が含まれている場合、更新時にそのIDが使用されます

### frontmatterの例

```markdown
---
docbase_id: 12345
---

# 記事タイトル
```

## 記事管理

- Obsidianによる記事の作成・編集
- カテゴリ別の記事整理
- 図表作成（Excalidraw）
- 画像管理
