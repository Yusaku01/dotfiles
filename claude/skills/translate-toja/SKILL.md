---
name: translate-toja
description: 英語のAstroドキュメントを日本語に翻訳する
argument-hint: [英語MDXファイルパス]
---

# 翻訳スキル

英語のAstro MDXファイルを日本語に翻訳し、レビュー用の並列形式で出力する。

## 手順

1. 引数で渡された英語MDXファイルを読み込む
2. パスの`en/`を`ja/`に置き換えた場所に出力ファイルを作成する
   - 例: `src/content/docs/en/guides/routing.mdx` → `src/content/docs/ja/guides/routing.mdx`
3. 既存の日本語ファイルがある場合は上書きする

## 出力形式

各段落（テキストブロック）について、原文をHTMLコメントで残し、直後に翻訳文を配置する。

```mdx
{/* EN: Astro provides built-in routing based on your file structure.  */}
Astroはファイル構造にもとづいたビルトインのルーティングを提供します。

{/* EN: Any `.astro` file in the `src/pages/` directory becomes a page.*/}
`src/pages/`ディレクトリ内の`.astro`ファイルはすべてページになります。
```

以下はコメント化せず、そのまま出力する:
- frontmatter（`---`で囲まれたブロック。ただしtitleやdescriptionなどのテキスト値は翻訳する）
- コードブロック（```で囲まれたブロック）
- JSX/Astroコンポーネント（`<ComponentName ... />`や`<ComponentName>...</ComponentName>`）
- import文
- 見出し（`#`, `##`, etc.）は翻訳するが、コメント化する

## 翻訳ルール

### 基本スタイル
- です・ます調を使用する
- 冗長表現を避ける
  - することができます → できます
  - 使うことができます → 使えます
- メニュー項目など表示領域が限られている箇所では文末の「する」を省略する
  - ただし「貢献する」のように目的語がなく「名詞+する」だけの場合は省略しない
- _イタリック_ は日本語として不自然になる場合は省略してよい

### スペースのルール
- 日本語と英数字の間にスペースを入れない
- インラインコード（`code`）の前後にもスペースを入れない
- 適合例: TypeScriptコードを書く予定がある場合は、Astroの`strict`テンプレートを使用します。
- 不適合例: TypeScript コードを書く予定がある場合は、Astro の `strict` テンプレートを使用します。
- ただし英語文脈の文章ではこの限りではない

### アンカー・記号
- ページ内アンカーは#構文の見出しを利用し、英単語は小文字にする
- 英文末の`:`は日本語では`。`に置き換える（ケースによっては`:`のまま）

### 翻訳の姿勢
- 無理に意訳せず、原文を尊重したストレート翻訳
- 意訳するくらいなら文節を区切る
- 少し淡白に感じるくらいでよい

## 用語集

| 英語 | 日本語 | 備考 |
|:-----|:-------|:-----|
| adapter | アダプター | |
| architecture | アーキテクチャ | 長音符号「ー」は不要 |
| build | ビルドする | Astroの生成工程を指す場合。構築などにしない |
| content collections | コンテンツコレクション | |
| contributor | コントリビューター | 動詞contributeは「貢献する」 |
| dependency | 依存関係 | |
| frontmatter | フロントマター | 概念を指す場合。プロパティ示す場合はfrontmatterのまま |
| integration | インテグレーション | Astroの機能を指す場合 |
| island(s) | アイランド | Astroアイランドを指す場合。島の意味ならそのまま「島」 |
| page | ページ | |
| partial hydration | パーシャルハイドレーション | |
| render | レンダリング（する） | |
| route | ルーティング | routeとrootを区別するため |
| Markdown | Markdown | カタカナ表記にしない |
| Issue | Issue | GitHub上のIssueはカタカナにしない |
| props/prop | props/prop | そのまま |
| collection loader | コレクションローダー | |
| Syntax | 構文 | |
| assertion | アサーション | |
| expectation | 期待値 | |

## 翻訳完了後

出力ファイルのパスを表示する。
