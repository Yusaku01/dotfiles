# GitHub 運用ルール

## Conventional Commits

### 基本形式
```
<type>: <description>

[optional body]

[optional footer(s)]
```

### コミットタイプ
- `feat`: 新機能の追加
- `fix`: バグ修正
- `docs`: ドキュメントの変更
- `style`: コードスタイルの変更（機能に影響しない）
- `refactor`: リファクタリング
- `test`: テストの追加・修正
- `chore`: ビルドプロセスやツールの変更

### コミット例
```bash
feat: add user authentication feature
fix: resolve login validation error
docs: update installation instructions
refactor: simplify error handling logic
test: add unit tests for login function
chore: update dependency versions
```

## ブランチ命名規則

### 基本パターン
```
<type>/<description>
```

### ブランチタイプ
- `feature/`: 新機能開発
- `fix/`: バグ修正
- `hotfix/`: 緊急修正
- `docs/`: ドキュメント更新
- `refactor/`: リファクタリング
- `style/`: UI修正
- `test/`: テスト改善

### 命名例
```
feature/user-authentication
fix/login-validation-bug
docs/api-documentation
refactor/database-optimization
```

## 基本的なGitワークフロー

### 1. 新しいブランチの作成
```bash
# mainブランチから最新の状態を取得
git checkout main
git pull origin main

# 新しいブランチを作成
git checkout -b feature/your-feature-name
```

### 2. 開発・コミット
```bash
# 変更を追加
git add .

# コミット（Conventional Commits準拠）
git commit -m "feat: add user authentication feature"

# リモートにプッシュ
git push -u origin feature/your-feature-name
```

### 3. 作業完了後のクリーンアップ
```bash
# mainブランチに戻る
git checkout main
git pull origin main

# 作業ブランチを削除
git branch -d feature/your-feature-name
```

## 注意事項
- mainブランチへの直接コミット禁止
- プッシュ前にローカルテストを実行
- 適切なコミットメッセージを使用
