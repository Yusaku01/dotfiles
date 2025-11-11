# 環境情報

## 技術スタック

- **Node.js**: JavaScript実行環境
- **ES Modules**: モダンなJavaScript構文（type: "module"）
- **node-fetch**: HTTP通信ライブラリ
- **dotenv**: 環境変数管理
- **Obsidian**: マークダウン編集環境
- **Excalidraw**: 図表作成ツール

## 環境変数設定

投稿を行うには、以下の環境変数が必要です：

```bash
# .envファイルに設定
DOCBASE_ACCESS_TOKEN=your_docbase_access_token
DOCBASE_TEAM=your_team_name
```

### アクセストークンの取得方法

1. Docbaseにログイン
2. チーム設定 > API > アクセストークンを生成
3. 生成されたトークンを`.env`ファイルに設定

## 依存関係

### インストール
```bash
npm install
```

### 使用ライブラリ
- `dotenv`: 環境変数管理
- `node-fetch`: HTTP通信ライブラリ
- `zenn-cli`: Zenn投稿用（オプション）

## プロジェクト設定

- プロジェクトはES Modules使用（type: "module"）
- 日本語記事が中心のため、ファイル名やコメントは日本語対応
- ビルド、テスト、リンティングプロセスは設定されていません（単純なCLIツール + 知識管理システムのため）
