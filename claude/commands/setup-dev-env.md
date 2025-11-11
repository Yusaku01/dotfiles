---
description: "プロジェクト名とフレームワークを指定して開発環境を自動セットアップ（Claude Code最適化版）"
allowed-tools: ["Bash", "LS", "Write", "MultiEdit"]
version: "2.0"
---

# Setup Development Environment (v2.0)

指定されたプロジェクト名とフレームワークに基づいて、`$HOME/web/` 配下に新しい開発環境を自動セットアップします。Claude Code環境に最適化され、実際の開発で必要な設定を含みます。

カスタマイズ可能な変数（環境変数で上書き可能）:
- `PROJECTS_BASE_DIR`（デフォルト: `$HOME/web`）
- `SUPPORTED_FRAMEWORKS`（スペース区切り、デフォルト: `nextjs react react-lib npm-package vue astro expo jquery`）

## 🚀 使用方法

```bash
/setup-dev-env [プロジェクト名] [フレームワーク名] [オプション]
```

## 📋 引数

- `[プロジェクト名]`: 作成するプロジェクトの名前（必須）
- `[フレームワーク名]`: 使用するフレームワーク（必須）
- `[オプション]`: 追加機能（オプション、複数指定可能）

## 🛠 対応フレームワーク

| フレームワーク名 | 説明 | セットアップ内容 |
|---|---|---|
| `nextjs` | Next.js + Tailwind CSS | create-next-app + 最新設定 |
| `react` | React.js (Vite) | Viteベースの React 環境 |
| `react-lib` | React Library | npmライブラリ用React環境 |
| `npm-package` | NPM Package | 汎用npmパッケージ環境 |
| `vue` | Vue.js + UnoCSS (Vite) | VueベースのVite環境 + UnoCSS |
| `astro` | Astro.js | 公式CLIウィザード |
| `expo` | Expo + Tamagui | Tamagui テンプレート使用 |
| `jquery` | jQuery.js | 最小npm環境 + jQuery |

## ⚙️ オプション機能

| オプション | 説明 |
|---|---|
| `--with-tests` | 包括的テスト環境（Vitest + Testing Library + jsdom） |
| `--lib-mode` | ライブラリビルド用設定（Vite library mode） |
| `--features-structure` | bulletproof-react準拠のディレクトリ構造 |
| `--with-mermaid` | Mermaid.js（ダイアグラム・チャート用） |
| `--typescript-strict` | 厳格なTypeScript設定 |

## 📖 使用例

```bash
# 基本的なNext.jsプロジェクト
/setup-dev-env my-blog nextjs

# テスト環境付きReactプロジェクト
/setup-dev-env todo-app react --with-tests

# Reactライブラリ（npmパッケージ）
/setup-dev-env my-library react-lib --lib-mode --with-tests --features-structure

# Mermaid付きアプリケーション
/setup-dev-env chart-app react --with-mermaid --with-tests

# 厳格TypeScript設定のnpmパッケージ
/setup-dev-env utils-package npm-package --typescript-strict --with-tests
```

---

🚀 Claude Code環境に最適化された開発環境セットアップを実行します。引数: $ARGUMENTS

## 🔍 引数解析とオプション処理

引数を解析してセットアップを開始します：

```bash
ARGS=($ARGUMENTS)
PROJECT_NAME="${ARGS[0]}"
FRAMEWORK="${ARGS[1]}"
BASE_DIR="${PROJECTS_BASE_DIR:-${HOME}/web}"
PROJECT_PATH="$BASE_DIR/$PROJECT_NAME"

# オプションフラグの初期化
WITH_TESTS=false
LIB_MODE=false
FEATURES_STRUCTURE=false
WITH_MERMAID=false
TYPESCRIPT_STRICT=false

# オプションの解析
for arg in "${ARGS[@]:2}"; do
  case "$arg" in
    --with-tests)
      WITH_TESTS=true
      echo "🧪 テスト環境を追加します"
      ;;
    --lib-mode)
      LIB_MODE=true
      echo "📦 ライブラリモードを有効化します"
      ;;
    --features-structure)
      FEATURES_STRUCTURE=true
      echo "🏢 featuresベース構造を作成します"
      ;;
    --with-mermaid)
      WITH_MERMAID=true
      echo "📊 Mermaid.jsを追加します"
      ;;
    --typescript-strict)
      TYPESCRIPT_STRICT=true
      echo "🔒 厳格TypeScript設定を有効化します"
      ;;
    *)
      echo "⚠️  未知のオプション: $arg"
      ;;
  esac
done
```

### ✅ 引数バリデーション
プロジェクト名とフレームワーク名の必須チェックを行います：

```bash
if [[ -z "$PROJECT_NAME" || -z "$FRAMEWORK" ]]; then
    echo "❌ エラー: プロジェクト名とフレームワーク名の両方を指定してください。"
    echo ""
    echo "📝 使用方法:"
    echo "  /setup-dev-env [プロジェクト名] [フレームワーク名] [オプション]"
    echo ""
    echo "🛠 対応フレームワーク:"
    echo "  nextjs, react, react-lib, npm-package, vue, astro, expo, jquery"
    echo ""
    echo "⚙️ 使用可能オプション:"
    echo "  --with-tests, --lib-mode, --features-structure, --with-mermaid, --typescript-strict"
    echo ""
    echo "📚 例:"
    echo "  /setup-dev-env my-lib react-lib --lib-mode --with-tests --features-structure"
    exit 1
fi
```

### 🔍 フレームワーク対応チェック
指定されたフレームワークが対応しているかチェックします：

```bash
SUPPORTED_FRAMEWORKS_STR="${SUPPORTED_FRAMEWORKS:-nextjs react react-lib npm-package vue astro expo jquery}"
SUPPORTED_FRAMEWORKS=($SUPPORTED_FRAMEWORKS_STR)
if [[ ! " ${SUPPORTED_FRAMEWORKS[@]} " =~ " ${FRAMEWORK} " ]]; then
    echo "❌ エラー: サポートされていないフレームワークです: $FRAMEWORK"
    echo ""
    echo "🛠 対応フレームワーク: ${SUPPORTED_FRAMEWORKS[*]}"
    echo ""
    echo "📚 推奨組み合わせ:"
    echo "  ライブラリ開発: react-lib --lib-mode --with-tests --features-structure"
    echo "  アプリ開発: react --with-tests --with-mermaid"
    echo "  npmパッケージ: npm-package --typescript-strict --with-tests"
    exit 1
fi

echo "🚀 $FRAMEWORK プロジェクト '$PROJECT_NAME' を作成中..."
```

## 📁 ディレクトリ準備と環境確認

ベースディレクトリの存在確認と作成を行います（Claude Code環境対応）：

```bash
echo "🔍 環境を確認中..."

# ベースディレクトリの存在確認
if [[ ! -d "$BASE_DIR" ]]; then
    echo "📁 ベースディレクトリを作成: $BASE_DIR"
    mkdir -p "$BASE_DIR"
else
    echo "✅ ベースディレクトリが存在: $BASE_DIR"
fi

# プロジェクトディレクトリの重複チェック
if [[ -d "$PROJECT_PATH" ]]; then
    echo "❌ エラー: プロジェクトディレクトリが既に存在します: $PROJECT_PATH"
    echo ""
    echo "📝 解決方法:"
    echo "  1. 別のプロジェクト名を使用する"
    echo "  2. 既存のディレクトリを削除する: rm -rf \"$PROJECT_PATH\""
    exit 1
fi

echo "✅ ディレクトリ確認完了"
echo "📍 作成予定地: $PROJECT_PATH"
echo ""
```

## 🎨 フレームワーク別セットアップ（Claude Code最適化）

### △ Next.js + Tailwind CSS

```bash
if [[ "$FRAMEWORK" == "nextjs" ]]; then
    echo "△ Next.js + Tailwind CSS プロジェクトを作成中..."
    
    # プロジェクト作成（Claude Code環境対応）
    npx create-next-app@latest "$PROJECT_PATH" --typescript --tailwind --eslint --app --src-dir --import-alias "@/*"
    
    # テスト環境の追加
    if [[ "$WITH_TESTS" == "true" ]]; then
        echo "🧪 テスト環境を追加中..."
        npm install --prefix "$PROJECT_PATH" -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom @vitejs/plugin-react
        
        # Vitest設定をNext.jsに追加
        cat >> "$PROJECT_PATH/vitest.config.ts" << 'EOF'
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom',
    globals: true,
    setupFiles: './src/test/setup.ts',
  },
})
EOF
        
        # テストセットアップファイル
        mkdir -p "$PROJECT_PATH/src/test"
        cat > "$PROJECT_PATH/src/test/setup.ts" << 'EOF'
import '@testing-library/jest-dom'
EOF
    fi
    
    echo "✅ Next.js + Tailwind CSS プロジェクトが正常に作成されました!"
    echo "📁 プロジェクトパス: $PROJECT_PATH"
    echo "🚀 開発サーバーを起動するには:"
    echo "   npm run dev --prefix \"$PROJECT_PATH\""
fi
```

### ⚛️ React.js (Vite) - アプリケーション開発用

```bash
if [[ "$FRAMEWORK" == "react" ]]; then
    echo "⚛️ React.js (Vite) プロジェクトを作成中..."
    
    # プロジェクト作成（Claude Code環境対応）
    npm create vite@latest "$PROJECT_PATH" -- --template react-ts
    
    # 依存関係のインストール（prefix使用）
    npm install --prefix "$PROJECT_PATH"
    
    # テスト環境のセットアップ
    if [[ "$WITH_TESTS" == "true" ]]; then
        echo "🧪 テスト環境をセットアップ中..."
        npm install --prefix "$PROJECT_PATH" -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom @vitest/ui
        
        # Vite設定更新
        cat >> "$PROJECT_PATH/vite.config.ts" << 'EOF'
/// <reference types="vitest" />
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/test/setup.ts',
  },
})
EOF
        
        # テストセットアップファイル作成
        mkdir -p "$PROJECT_PATH/src/test"
        cat > "$PROJECT_PATH/src/test/setup.ts" << 'EOF'
import '@testing-library/jest-dom'
import { cleanup } from '@testing-library/react'
import { afterEach } from 'vitest'

afterEach(() => {
  cleanup()
})
EOF
    fi
    
    # Mermaidの追加
    if [[ "$WITH_MERMAID" == "true" ]]; then
        echo "📊 Mermaid.jsを追加中..."
        npm install --prefix "$PROJECT_PATH" mermaid
    fi
    
    # features構造の作成
    if [[ "$FEATURES_STRUCTURE" == "true" ]]; then
        echo "🏢 featuresベースのディレクトリ構造を作成中..."
        mkdir -p "$PROJECT_PATH/src/features"
        mkdir -p "$PROJECT_PATH/src/lib"
        mkdir -p "$PROJECT_PATH/src/types"
        mkdir -p "$PROJECT_PATH/src/test"
    fi
    
    echo "✅ React.js (Vite) プロジェクトが正常に作成されました!"
    echo "📁 プロジェクトパス: $PROJECT_PATH"
    echo "🚀 開発サーバーを起動するには:"
    echo "   npm run dev --prefix \"$PROJECT_PATH\""
fi
```

### 📦 React Library - npmライブラリ用

```bash
if [[ "$FRAMEWORK" == "react-lib" ]]; then
    echo "📦 React Library プロジェクトを作成中..."
    
    # プロジェクト作成
    npm create vite@latest "$PROJECT_PATH" -- --template react-ts
    
    # 依存関係のインストール
    npm install --prefix "$PROJECT_PATH"
    
    # ライブラリ用追加依存関係
    npm install --prefix "$PROJECT_PATH" -D @types/node vite-plugin-dts
    
    # テスト環境はデフォルトで追加
    echo "🧪 ライブラリ用テスト環境をセットアップ中..."
    npm install --prefix "$PROJECT_PATH" -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom @vitest/ui
    
    # ライブラリ用vite.config.ts
    cat > "$PROJECT_PATH/vite.config.ts" << 'EOF'
/// <reference types="vitest" />
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { fileURLToPath, URL } from "node:url";
import dts from "vite-plugin-dts";

export default defineConfig({
  plugins: [
    react(),
    dts({
      entryRoot: "src",
      outDir: "dist",
      insertTypesEntry: true,
      exclude: [
        "src/**/__tests__/**",
        "src/test/**",
        "src/**/*.test.ts",
        "src/**/*.test.tsx",
      ],
    }),
  ],
  resolve: {
    alias: {
      "@": fileURLToPath(new URL("./src", import.meta.url)),
    },
  },
  test: {
    globals: true,
    environment: "jsdom",
    setupFiles: "./src/test/setup.ts",
  },
  build: {
    lib: {
      entry: fileURLToPath(new URL("src/index.ts", import.meta.url)),
      name: "$PROJECT_NAME",
      fileName: (format) => `${PROJECT_NAME}.${format}.js`,
    },
    rollupOptions: {
      external: ["react", "react-dom"],
      output: {
        globals: {
          react: "React",
          "react-dom": "ReactDOM",
        },
      },
    },
  },
});
EOF
    
    # ライブラリ用package.json更新
    cat > "$PROJECT_PATH/package.json" << EOF
{
  "name": "$PROJECT_NAME",
  "version": "0.1.0",
  "description": "React library",
  "type": "module",
  "main": "./dist/$PROJECT_NAME.umd.js",
  "module": "./dist/$PROJECT_NAME.es.js",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "import": "./dist/$PROJECT_NAME.es.js",
      "require": "./dist/$PROJECT_NAME.umd.js"
    }
  },
  "files": [
    "dist"
  ],
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "lint": "eslint .",
    "preview": "vite preview",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest --coverage",
    "prepublishOnly": "npm run build"
  },
  "peerDependencies": {
    "react": ">=16.8.0",
    "react-dom": ">=16.8.0"
  },
  "keywords": [
    "react",
    "library",
    "typescript"
  ],
  "author": "",
  "license": "MIT"
}
EOF
    
    # features構造はデフォルトで作成
    echo "🏢 bulletproof-react準拠のディレクトリ構造を作成中..."
    mkdir -p "$PROJECT_PATH/src/features"
    mkdir -p "$PROJECT_PATH/src/lib"
    mkdir -p "$PROJECT_PATH/src/types"
    mkdir -p "$PROJECT_PATH/src/test"
    
    # テストセットアップファイル作成
    cat > "$PROJECT_PATH/src/test/setup.ts" << 'EOF'
import '@testing-library/jest-dom'
import { cleanup } from '@testing-library/react'
import { afterEach } from 'vitest'

afterEach(() => {
  cleanup()
})
EOF
    
    # メインエクスポートファイル
    cat > "$PROJECT_PATH/src/index.ts" << 'EOF'
// Export your components and utilities here
export * from './types'
EOF
    
    # 型定義ファイル
    cat > "$PROJECT_PATH/src/types/index.ts" << 'EOF'
// Add your type definitions here
EOF
    
    echo "✅ React Library プロジェクトが正常に作成されました!"
    echo "📁 プロジェクトパス: $PROJECT_PATH"
    echo "🚀 開発サーバーを起動するには:"
    echo "   npm run dev --prefix \"$PROJECT_PATH\""
    echo "📝 ビルドするには:"
    echo "   npm run build --prefix \"$PROJECT_PATH\""
fi
```

### 📦 NPM Package - 汎用npmパッケージ用

```bash
if [[ "$FRAMEWORK" == "npm-package" ]]; then
    echo "📦 NPM Package プロジェクトを作成中..."
    
    # プロジェクトディレクトリ作成
    mkdir -p "$PROJECT_PATH/src"
    mkdir -p "$PROJECT_PATH/dist"
    
    # package.json作成
    cat > "$PROJECT_PATH/package.json" << EOF
{
  "name": "$PROJECT_NAME",
  "version": "0.1.0",
  "description": "NPM package",
  "type": "module",
  "main": "./dist/index.js",
  "module": "./dist/index.es.js",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "import": "./dist/index.es.js",
      "require": "./dist/index.js"
    }
  },
  "files": [
    "dist"
  ],
  "scripts": {
    "build": "tsc && vite build",
    "dev": "tsc --watch",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "lint": "eslint src",
    "prepublishOnly": "npm run build"
  },
  "keywords": [
    "typescript",
    "npm-package"
  ],
  "author": "",
  "license": "MIT"
}
EOF
    
    # TypeScript設定
    if [[ "$TYPESCRIPT_STRICT" == "true" ]]; then
        cat > "$PROJECT_PATH/tsconfig.json" << 'EOF'
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020"],
    "module": "ESNext",
    "moduleResolution": "node",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "exactOptionalPropertyTypes": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "outDir": "./dist",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*"],
  "exclude": ["dist", "node_modules", "**/*.test.ts"]
}
EOF
    else
        cat > "$PROJECT_PATH/tsconfig.json" << 'EOF'
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020"],
    "module": "ESNext",
    "moduleResolution": "node",
    "strict": true,
    "declaration": true,
    "outDir": "./dist",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*"],
  "exclude": ["dist", "node_modules", "**/*.test.ts"]
}
EOF
    fi
    
    # 基本依存関係インストール
    npm install --prefix "$PROJECT_PATH" -D typescript vite @types/node
    
    # テスト環境
    if [[ "$WITH_TESTS" == "true" ]]; then
        echo "🧪 テスト環境をセットアップ中..."
        npm install --prefix "$PROJECT_PATH" -D vitest @vitest/ui jsdom
        
        # vite.config.ts
        cat > "$PROJECT_PATH/vite.config.ts" << 'EOF'
/// <reference types="vitest" />
import { defineConfig } from 'vite'
import { resolve } from 'path'

export default defineConfig({
  test: {
    globals: true,
    environment: 'jsdom',
  },
  build: {
    lib: {
      entry: resolve(__dirname, 'src/index.ts'),
      name: 'MyPackage',
      fileName: (format) => `index.${format}.js`,
    },
    rollupOptions: {
      external: [],
    },
  },
})
EOF
    fi
    
    # メインエントリファイル
    cat > "$PROJECT_PATH/src/index.ts" << 'EOF'
/**
 * Main entry point for the package
 */

export function hello(name: string): string {
  return `Hello, ${name}!`
}

// Export your functions and classes here
EOF
    
    # README.md作成
    cat > "$PROJECT_PATH/README.md" << EOF
# $PROJECT_NAME

Description of your npm package.

## Installation

\`\`\`bash
npm install $PROJECT_NAME
\`\`\`

## Usage

\`\`\`typescript
import { hello } from '$PROJECT_NAME'

console.log(hello('World'))
\`\`\`

## Development

\`\`\`bash
npm install
npm run dev
npm run test
npm run build
\`\`\`

## License

MIT
EOF
    
    echo "✅ NPM Package プロジェクトが正常に作成されました!"
    echo "📁 プロジェクトパス: $PROJECT_PATH"
    echo "🚀 開発を開始するには:"
    echo "   npm install --prefix \"$PROJECT_PATH\""
    echo "   npm run dev --prefix \"$PROJECT_PATH\""
    echo "📝 ビルドするには:"
    echo "   npm run build --prefix \"$PROJECT_PATH\""
fi
```

### 💚 Vue.js + UnoCSS (Vite)

```bash
if [[ "$FRAMEWORK" == "vue" ]]; then
    echo "💚 Vue.js + UnoCSS (Vite) プロジェクトを作成中..."
    
    # プロジェクト作成（最新オプション使用）
    npm create vue@latest "$PROJECT_PATH" -- --typescript --jsx --router --pinia --vitest --eslint --prettier
    
    # 依存関係インストール
    npm install --prefix "$PROJECT_PATH"
    
    # UnoCSSを追加
    npm install --prefix "$PROJECT_PATH" -D unocss
    
    # UnoCSS設定を自動追加
    cat >> "$PROJECT_PATH/vite.config.ts" << 'EOF'
import UnoCSS from 'unocss/vite'

// Add UnoCSS to plugins array
// plugins: [vue(), UnoCSS()]
EOF
    
    echo "✅ Vue.js + UnoCSS (Vite) プロジェクトが正常に作成されました!"
    echo "📁 プロジェクトパス: $PROJECT_PATH"
    echo "🚀 開発サーバーを起動するには:"
    echo "   npm run dev --prefix \"$PROJECT_PATH\""
    echo "📝 UnoCSS設定を手動で vite.config.ts に追加してください"
fi
```

### 🚀 Astro.js

```bash
if [[ "$FRAMEWORK" == "astro" ]]; then
    echo "🚀 Astro.js プロジェクトを作成中..."
    
    # プロジェクト作成（デフォルトテンプレート使用）
    npm create astro@latest "$PROJECT_PATH" -- --template minimal --typescript strict --install --git
    
    # テスト環境の追加
    if [[ "$WITH_TESTS" == "true" ]]; then
        echo "🧪 テスト環境を追加中..."
        npm install --prefix "$PROJECT_PATH" -D vitest @vitest/ui jsdom
    fi
    
    echo "✅ Astro.js プロジェクトが正常に作成されました!"
    echo "📁 プロジェクトパス: $PROJECT_PATH"
    echo "🚀 開発サーバーを起動するには:"
    echo "   npm run dev --prefix \"$PROJECT_PATH\""
fi
```

### 📱 Expo + Tamagui

```bash
if [[ "$FRAMEWORK" == "expo" ]]; then
    echo "📱 Expo + Tamagui プロジェクトを作成中..."
    
    # Corepack有効化
    corepack enable
    
    # プロジェクト作成（絶対パス指定）
    yarn create tamagui@latest --template expo-router "$PROJECT_PATH"
    
    # Yarnを安定版に（絶対パスで実行）
    cd "$PROJECT_PATH" && yarn set version stable && yarn
    
    echo "✅ Expo + Tamagui プロジェクトが正常に作成されました!"
    echo "📁 プロジェクトパス: $PROJECT_PATH"
    echo "🚀 開発サーバーを起動するには:"
    echo "   cd \"$PROJECT_PATH\" && yarn web"
    echo "   📱 モバイル: yarn ios または yarn android"
fi
```

### 📜 jQuery.js

```bash
if [[ "$FRAMEWORK" == "jquery" ]]; then
    echo "📜 jQuery.js プロジェクトを作成中..."
    
    # プロジェクトディレクトリ作成
    mkdir -p "$PROJECT_PATH"
    
    # npm初期化とjQueryインストール（prefix使用）
    npm init -y --prefix "$PROJECT_PATH"
    npm install --prefix "$PROJECT_PATH" jquery
    npm install --prefix "$PROJECT_PATH" -D @types/jquery typescript
    
    # 基本的なindex.htmlファイルを作成
    cat > "$PROJECT_PATH/index.html" << 'EOF'
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>jQuery Project</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        #demo { cursor: pointer; color: #007bff; }
        #demo:hover { text-decoration: underline; }
    </style>
</head>
<body>
    <h1>📜 jQuery Project</h1>
    <p id="demo">Hello jQuery! (Click me)</p>
    
    <script src="node_modules/jquery/dist/jquery.min.js"></script>
    <script>
        $(document).ready(function() {
            $("#demo").click(function() {
                $(this).text("jQuery is working! 🎉");
            });
        });
    </script>
</body>
</html>
EOF
    
    # README.md作成
    cat > "$PROJECT_PATH/README.md" << EOF
# $PROJECT_NAME

Simple jQuery project setup.

## Usage

Open \`index.html\` in your browser to see jQuery in action.

## Development

1. Make changes to \`index.html\`
2. Refresh your browser
3. That's it!
EOF
    
    echo "✅ jQuery.js プロジェクトが正常に作成されました!"
    echo "📁 プロジェクトパス: $PROJECT_PATH"
    echo "🚀 動作確認するには:"
    echo "   open \"$PROJECT_PATH/index.html\""
fi
```

## 🎆 セットアップ完了と次のステップ

```bash
echo ""
echo "============================================"
echo "🎉 プロジェクト '$PROJECT_NAME' ($FRAMEWORK) のセットアップが完了しました!"
echo "============================================"
echo ""
echo "📍 プロジェクト情報:"
echo "  名前: $PROJECT_NAME"
echo "  種類: $FRAMEWORK"
echo "  場所: $PROJECT_PATH"
echo ""

# 有効なオプションの表示
if [[ "$WITH_TESTS" == "true" || "$LIB_MODE" == "true" || "$FEATURES_STRUCTURE" == "true" || "$WITH_MERMAID" == "true" || "$TYPESCRIPT_STRICT" == "true" ]]; then
    echo "⚙️ 有効なオプション:"
    [[ "$WITH_TESTS" == "true" ]] && echo "  ✅ テスト環境 (Vitest + Testing Library)"
    [[ "$LIB_MODE" == "true" ]] && echo "  ✅ ライブラリモード (Vite library build)"
    [[ "$FEATURES_STRUCTURE" == "true" ]] && echo "  ✅ featuresベース構造"
    [[ "$WITH_MERMAID" == "true" ]] && echo "  ✅ Mermaid.js"
    [[ "$TYPESCRIPT_STRICT" == "true" ]] && echo "  ✅ 厳格TypeScript設定"
    echo ""
fi

echo "🚀 次のステップ:"
echo "  1. Claude Code環境での作業:"
echo "     - ファイル操作は --prefix オプションを使用"
echo "     - npm run dev --prefix \"$PROJECT_PATH\""
echo "     - npm run build --prefix \"$PROJECT_PATH\""
echo ""
echo "  2. ローカル環境での作業:"
echo "     cd \"$PROJECT_PATH\""
echo "     npm run dev"
echo ""
echo "  3. コードエディタでプロジェクトを開く"

# フレームワーク固有のアドバイス
case "$FRAMEWORK" in
    "react-lib")
        echo ""
        echo "📦 ライブラリ開発のヒント:"
        echo "  - src/index.ts がメインエクスポートポイント"
        echo "  - features/ ディレクトリで機能を管理"
        echo "  - npm run test でテスト実行"
        echo "  - npm run build でライブラリビルド"
        ;;
    "npm-package")
        echo ""
        echo "📦 npmパッケージ開発のヒント:"
        echo "  - package.json の name を更新してください"
        echo "  - npm publish で公開できます"
        echo "  - README.md を充実させてください"
        ;;
esac

echo ""
echo "📚 参考リンク:"
echo "  - Claude Codeドキュメント: https://docs.anthropic.com/en/docs/claude-code"
echo "  - トラブルシューティング: /setup-dev-env --help"
echo ""
```

---

## 🛠 トラブルシューティングとFAQ

### よくある問題

#### “cd: permission denied” エラー
**原因**: Claude Codeのディレクトリ制限  
**解決方法**: `--prefix` オプションを使用
```bash
npm run dev --prefix "/path/to/project"
```

#### パッケージインストールが失敗する
**原因**: ネットワークか権限の問題  
**解決方法**:
1. ネットワーク接続を確認
2. npm cacheをクリア: `npm cache clean --force`
3. 再実行

#### テストが動作しない
**原因**: テスト環境の設定不備  
**解決方法**: `--with-tests` オプションで再作成

#### ライブラリビルドが失敗する
**原因**: vite.config.ts の設定不備  
**解決方法**: `react-lib` タイプで再作成

### 推奨ワークフロー

#### ライブラリ開発
```bash
/setup-dev-env my-library react-lib --lib-mode --with-tests --features-structure
```

#### アプリケーション開発
```bash
/setup-dev-env my-app react --with-tests --with-mermaid
```

#### npmパッケージ作成
```bash
/setup-dev-env my-package npm-package --typescript-strict --with-tests
```

### サポートされる組み合わせ

- ✅ react + --with-tests + --features-structure
- ✅ react-lib + --lib-mode + --with-tests
- ✅ nextjs + --with-tests
- ✅ npm-package + --typescript-strict + --with-tests
- ✅ すべてのフレームワーク + --with-mermaid

### 更新履歴

- v2.0: Claude Code最適化、新プロジェクトタイプ追加
- v1.0: 初期バージョン
