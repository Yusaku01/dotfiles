---
description: "既存記事の文体・構成を分析し、指定記事に適用するコマンド"
allowed-tools: ["Bash", "Read", "Write", "Edit", "Task"]
---

# Apply Writing Style

既存記事の文体・構成を分析し、指定記事に適用するコマンドです。

## 使用方法

```bash
/apply-writing-style <記事パス> [オプション]
```

## 引数

- `<記事パス>`: 文体を適用する対象記事のパス（必須）

## オプション

- `--structure`: 構成パターンの適用（見出し、セクション、流れ）
- `--tone`: トーンと声のパターン適用
- `--format`: フォーマットパターン適用（コードブロック、リスト、強調）
- `--all`: 全ての文体要素を適用（デフォルト）
- `--fetch`: 記事情報を新規取得（gemini-cli使用）

## 使用例

```bash
# 全ての文体要素を適用（保存済みパターンを使用）
/apply-writing-style ./note/about-frontend/new-article.md

# 構成のみ適用
/apply-writing-style ./note/about-frontend/new-article.md --structure

# トーンとフォーマットのみ適用
/apply-writing-style ./note/about-frontend/new-article.md --tone --format

# 最新の記事から文体パターンを取得して適用
/apply-writing-style ./note/about-frontend/new-article.md --fetch
```

## 機能詳細

1. **参考記事分析**:
   - 記事リンクファイル（`./note/articles-reference/Sakuのこれまで書いた記事のリンク.md`）から文体パターンを読み込み
   - `--fetch`オプション指定時のみ、gemini-cliで記事を新規取得・分析
   - 分析済みの文体パターン：トーン、構成、フォーマット、文章長など

2. **文体適用**:
   - 対象記事のパスを引数として受け取り
   - 学習した文体パターンを対象記事に適用
   - 元の内容を保持しながら文体・構成を調整

3. **バックアップ機能**:
   - 変換前の記事をバックアップ保存
   - 適用後の詳細分析レポート生成

---

引数: $ARGUMENTS

## 実装開始

引数を解析して文体適用処理を実行します。

### 1. 引数解析とオプション処理

```bash
# 引数の解析
args=($ARGUMENTS)
targetArticlePath="${args[0]}"
applyStructure=false
applyTone=false
applyFormat=false
applyAll=true
fetchNewArticles=false

# オプション解析
for arg in "${args[@]:1}"; do
    case "$arg" in
        --structure)
            applyStructure=true
            applyAll=false
            ;;
        --tone)
            applyTone=true
            applyAll=false
            ;;
        --format)
            applyFormat=true
            applyAll=false
            ;;
        --all)
            applyAll=true
            ;;
        --fetch)
            fetchNewArticles=true
            ;;
    esac
done

# デフォルト設定
if [ "$applyAll" = true ]; then
    applyStructure=true
    applyTone=true
    applyFormat=true
fi
```

### 2. 引数検証

対象記事パスが指定されているかチェックします。

```bash
if [ -z "$targetArticlePath" ]; then
    echo "エラー: 対象記事のパスを指定してください"
    echo "使用方法: /apply-writing-style <記事パス> [オプション]"
    exit 1
fi

if [ ! -f "$targetArticlePath" ]; then
    echo "エラー: 指定された記事ファイルが存在しません: $targetArticlePath"
    exit 1
fi
```

### 3. 文体パターンの取得

**文体パターンの取得処理**:

1. デフォルト動作（`--fetch`なし）:
   - `./note/articles-reference/Sakuのこれまで書いた記事のリンク.md`から分析済みの文体パターンを読み込み
   - 高速に処理を実行

2. `--fetch`オプション指定時:
   - 記事URLリストからgemini-cliを使用して最新の記事内容を取得
   - 文体パターンを新規分析し、記事リンクファイルを更新

### 4. 文体パターンの適用準備

**適用する文体パターン**（記事リンクファイルから取得）：

1. **構成パターン**:
   - 具体的で簡潔な見出し設定
   - 論理的な見出し階層（大見出し→中見出し→小見出し）
   - 導入→本文→まとめの明確な流れ

2. **トーンパターン**:
   - です・ます調で統一
   - 読者に寄り添う語りかけ（問題提起→解決策の提示）
   - 親しみやすく誠実なトーン

3. **フォーマットパターン**:
   - コード例の前後に必ず説明文を配置
   - 問題コードと解決コードの対比
   - 適度な改行と箇条書きの活用

### 5. 対象記事の読み込みと分析

**対象記事の読み込み**を実行します：

対象記事を読み込み、現在の文体を分析します。

### 6. 文体適用処理

**文体適用処理**を実行します：

保存された文体パターンを対象記事に適用し、以下を調整します：

- **コード例の改善**: 各コード例の前後に説明文を追加
- **段落の調整**: 数行程度で読みやすく分割
- **トーンの統一**: です・ます調への統一、読者への語りかけ調整
- **見出しの改善**: 具体的で簡潔な見出しへの変更

### 7. バックアップ作成と結果保存

**バックアップ作成**を実行します：

変換前の記事をバックアップし、新しい文体で記事を更新します。

### 8. 結果レポート

**結果レポート**を表示します：

適用した変更の詳細レポートを表示します。

## 注意事項

- 対象記事のパスは必須です
- 参考記事リンクファイルが存在しない場合はエラーになります
- Web記事の取得に失敗した場合は警告を表示し、取得できた記事のみで分析を行います
- 変換前の記事は自動的にバックアップされます

## エラーハンドリング

### 入力検証エラー
- 対象記事パスが未指定の場合
- 対象記事ファイルが存在しない場合
- 無効なオプション指定の場合
- 読み書き権限がない場合

### ファイル処理エラー
- 参考記事リンクファイルが存在しない場合
- 参考記事リンクファイルの読み込みに失敗した場合
- 対象記事のバックアップ作成に失敗した場合
- 対象記事の更新に失敗した場合

### Web記事取得エラー
- Web記事の取得に失敗した場合
- 記事コンテンツの解析に失敗した場合
- 取得できた記事が0件の場合

### 詳細なエラーメッセージ
各エラーに対して具体的な解決方法を提示します：

```bash
# 例：対象記事が存在しない場合
echo "エラー: 指定された記事ファイルが存在しません"
echo "ファイル: $targetArticlePath"
echo "解決方法: 正しいファイルパスを指定してください"
```

## 高度なオプション機能

### 詳細設定オプション
- `--preview`: 適用前にプレビューを表示
- `--backup-dir`: バックアップファイルの保存先を指定
- `--verbose`: 詳細な処理ログを表示
- `--dry-run`: 実際の変更を行わずに処理内容を確認

### 使用例（高度なオプション）
```bash
# プレビュー機能付きで実行
/apply-writing-style ./note/article.md --preview

# カスタムバックアップディレクトリを指定
/apply-writing-style ./note/article.md --backup-dir ./backups/

# 詳細ログ付きで実行
/apply-writing-style ./note/article.md --verbose

# ドライランモードで実行
/apply-writing-style ./note/article.md --dry-run
```

## 設定ファイル対応

コマンドの設定を外部ファイルで管理できます：

```json
{
  "referenceFile": "./note/articles-reference/Sakuのこれまで書いた記事のリンク.md",
  "defaultOptions": {
    "applyStructure": true,
    "applyTone": true,
    "applyFormat": true
  },
  "backupDirectory": "./backups/",
  "verbose": false
}
```

以上の機能を実装し、あなたの記事執筆スタイルを効率的に適用できるツールを提供します。

## 実装実行

文体適用処理を開始します：

### Phase 1: 設定と検証

対象記事パスと適用オプションを確認します。

### Phase 2: 参考記事の分析

参考記事リンクファイルから記事を取得し、文体パターンを学習します。

### Phase 3: 文体適用

学習した文体パターンを対象記事に適用します。

### Phase 4: 結果の保存とレポート

変換結果を保存し、適用内容をレポートします。