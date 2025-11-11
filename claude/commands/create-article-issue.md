---
description: "記事作成・更新のためのGitHub Issue自動作成コマンド"
allowed-tools: ["Bash", "Read", "LS"]
---

# Create Article Issue

記事作成・更新のためのGitHub Issueを自動作成するコマンドです。既存記事の更新または新規記事作成に必要なタスクリストを含むIssueを生成します。

## 使用方法

```bash
/create-article-issue <ファイルパス> [オプション]
```

## 引数

- `<ファイルパス>`: 更新または作成する記事ファイルのパス（必須）
  - 例: `./note/about-frontend/TailwindCSSでコンテナクエリを使う.md`

## オプション

- `--title <タイトル>`: Issue タイトルをカスタマイズ（デフォルト: "docs: [記事テーマ]についての記事作成"）
- `--label <ラベル>`: 追加ラベルを指定（デフォルト: documentation,enhancement）
- `--assignee <ユーザー名>`: Issueのアサイン先を指定
- `--milestone <番号>`: マイルストーンを設定
- `--skip-confirmation`: 確認なしでIssueを作成

## 使用例

```bash
# 基本的な使用
/create-article-issue ./note/about-frontend/TailwindCSSでコンテナクエリを使う.md

# カスタムタイトルを指定
/create-article-issue ./note/about-frontend/ReactHooks.md --title "feat: React Hooksの詳細解説記事"

# ラベルとアサインを指定
/create-article-issue ./note/backend/GraphQL入門.md --label "backend,graphql" --assignee username

# 確認なしで作成
/create-article-issue ./note/about-frontend/新機能.md --skip-confirmation
```

## 機能詳細

### 1. ファイル情報の自動抽出

- **記事テーマ**: ファイル名から自動抽出（拡張子を除く）
- **配置ディレクトリ**: ファイルパスからディレクトリ名を抽出
- **ファイル存在確認**: 既存ファイルの更新か新規作成かを判定

### 2. Issue本文の自動生成

以下のタスクリストを自動生成：
1. 記事をすぐ手を動かして導入できるように書き込む
2. 更新ファイルが存在しない場合は新規作成
3. 既存の目次内容は最低限触れて作成
4. /gemini-searchコマンドで調査
5. 検証が必要な箇所は「検証が必要」と記載
6. /apply-writing-styleコマンドで文体調整
7. /git-workflow-setupコマンドでGit運用
8. 文字化け防止に注意

### 3. 自動化された処理

- Conventional Commits準拠のタイトル生成
- 適切なラベル設定（documentation, enhancement）
- Issue本文の最後に必ず「@claude 上記のタスクについてultrathinkで進めて」を追加

---

引数: $ARGUMENTS

## Issue作成実行

記事作成・更新のためのGitHub Issue作成を開始します。

### Phase 1: 引数解析と検証

まず、引数とオプションを解析します。

**引数解析を実行します:**

```bash
args=($ARGUMENTS)
filePath="${args[0]}"
customTitle=""
customLabels=""
assignee=""
milestone=""
skipConfirmation=false

# オプション解析
for i in "${!args[@]}"; do
    case "${args[i]}" in
        --title) customTitle="${args[i+1]}" ;;
        --label) customLabels="${args[i+1]}" ;;
        --assignee) assignee="${args[i+1]}" ;;
        --milestone) milestone="${args[i+1]}" ;;
        --skip-confirmation) skipConfirmation=true ;;
    esac
done
```

### Phase 2: ファイル情報の抽出

**ファイル情報を解析します:**

```bash
# 引数チェック
if [ -z "$filePath" ]; then
    echo "エラー: ファイルパスが指定されていません"
    echo "使用方法: /create-article-issue <ファイルパス> [オプション]"
    exit 1
fi

# ファイル名と拡張子の分離
fileName=$(basename "$filePath")
fileNameWithoutExt="${fileName%.md}"
articleTheme="$fileNameWithoutExt"

# ディレクトリ名の取得
dirPath=$(dirname "$filePath")
dirName=$(basename "$dirPath")

# ファイル存在確認
if [ -f "$filePath" ]; then
    fileExists=true
    echo "✓ 既存ファイルを更新: $filePath"
else
    fileExists=false
    echo "✓ 新規ファイルを作成: $filePath"
fi
```

### Phase 3: Issueタイトルの生成

**Issueタイトルを生成します:**

```bash
if [ -z "$customTitle" ]; then
    issueTitle="docs: ${articleTheme}についての記事作成"
else
    issueTitle="$customTitle"
fi

echo "Issueタイトル: $issueTitle"
```

### Phase 4: ラベル設定

**ラベルを設定します:**

```bash
# デフォルトラベル
defaultLabels="documentation,enhancement"

if [ -n "$customLabels" ]; then
    labels="$defaultLabels,$customLabels"
else
    labels="$defaultLabels"
fi

echo "ラベル: $labels"
```

### Phase 5: Issue本文の生成

**Issue本文を生成します:**

```bash
issueBody="## 概要
${articleTheme}に関する説明記事を作成します。

## タスク
- [ ] ${articleTheme}について、記事を見たらすぐ手を動かして導入出来るように、${dirName}/から${fileNameWithoutExt}.mdファイルを探してその中に書き込んで
- [ ] ${articleTheme}についての内容を書く更新ファイルが存在しない場合は、note/の適切なディレクトリ内に.mdファイルを新規作成しつつ記事を作成して
- [ ] 同.mdファイル内に書かれている既存の目次に関する内容は最低限触れて作成して
- [ ] しっかり調査して内容を書いて、調査は/gemini-searchコマンドを使用して
- [ ] 検証が必要な箇所は言い切らないで、検証が必要と書いて
- [ ] 最後の文体や構造調整は /apply-writing-styleコマンドを使用して
- [ ] Git運用は/git-workflow-setupコマンドを利用して
- [ ] 文字化けしないように気をつけて

## 記事で扱う内容
- ${articleTheme}の基本概念
- 実装方法と使用方法
- 実践的な使用例とコード
- パフォーマンスの考慮点
- ブラウザ対応状況

@claude 上記のタスクについてultrathinkで進めて"
```

### Phase 6: 環境確認

**GitHub CLI環境を確認します:**

```bash
# Gitリポジトリ確認
if ! git rev-parse --git-dir > /dev/null 2>&1; then
    echo "エラー: Gitリポジトリではありません"
    exit 1
fi

# GitHub CLI確認
if ! command -v gh &> /dev/null; then
    echo "エラー: GitHub CLI (gh) が見つかりません"
    echo "インストール: https://cli.github.com/"
    exit 1
fi

# 認証確認
if ! gh auth status &> /dev/null; then
    echo "エラー: GitHub CLIが未認証です"
    echo "認証: gh auth login"
    exit 1
fi
```

### Phase 7: Issue作成の実行

**GitHub Issueを作成します:**

```bash
echo ""
echo "=== Issue作成内容 ==="
echo "タイトル: $issueTitle"
echo "ラベル: $labels"
[ -n "$assignee" ] && echo "アサイン: $assignee"
[ -n "$milestone" ] && echo "マイルストーン: $milestone"
echo ""

# 確認プロンプト
if [ "$skipConfirmation" != true ]; then
    echo "Issueを作成しますか？ (Y/n)"
    read -r confirm
    if [ "$confirm" = "n" ] || [ "$confirm" = "N" ]; then
        echo "Issue作成をキャンセルしました"
        exit 0
    fi
fi

# Issueコマンド構築
createCommand="gh issue create --title \"$issueTitle\" --body \"$issueBody\" --label \"$labels\""

[ -n "$assignee" ] && createCommand="$createCommand --assignee \"$assignee\""
[ -n "$milestone" ] && createCommand="$createCommand --milestone \"$milestone\""

# Issue作成実行
echo "Issue作成中..."
if eval "$createCommand"; then
    echo ""
    echo "✅ Issue作成完了"
    
    # 作成されたIssue URLを取得
    issueUrl=$(gh issue list --limit 1 --json url --jq '.[0].url')
    if [ -n "$issueUrl" ]; then
        echo "URL: $issueUrl"
        
        echo ""
        echo "ブラウザで開きますか？ (y/N)"
        read -r openBrowser
        if [ "$openBrowser" = "y" ] || [ "$openBrowser" = "Y" ]; then
            gh issue view --web
        fi
    fi
else
    echo "❌ Issue作成失敗"
    exit 1
fi
```

### Phase 8: 完了レポート

**作成完了レポートを表示します:**

```bash
echo ""
echo "=== Issue作成完了 ==="
echo "次のステップ:"
echo "1. Issueの内容を確認"
echo "2. 必要に応じて追加情報を記載"
echo "3. '@claude ultrathink'でタスク実行を開始"
echo ""
echo "便利なコマンド:"
echo "- Issue一覧: gh issue list"
echo "- Issue詳細: gh issue view [番号]"
echo "- Issue編集: gh issue edit [番号]"
```

## エラーハンドリング

### 入力検証エラー
- ファイルパス未指定
- 無効なオプション
- GitHub CLI未認証

### Git操作エラー
- 非Gitリポジトリ
- GitHub接続エラー
- 権限不足

### Issue作成エラー
- 同名Issue存在
- ラベル不存在
- マイルストーン不存在

## 高度な機能

### バッチ処理
複数の記事ファイルを一括でIssue化：
```bash
for file in ./note/about-frontend/*.md; do
    /create-article-issue "$file" --skip-confirmation
done
```

### テンプレートカスタマイズ
`.claude/templates/article-issue.md`でテンプレートをカスタマイズ可能

### 自動ラベリング
ディレクトリ名に基づく自動ラベル設定：
- `about-frontend/` → frontend ラベル
- `backend/` → backend ラベル
- `infrastructure/` → infrastructure ラベル

このコマンドにより、記事作成・更新作業を効率的にIssue化し、タスク管理を自動化できます。