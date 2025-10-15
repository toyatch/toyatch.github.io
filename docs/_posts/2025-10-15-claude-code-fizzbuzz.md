---
layout: post
title:  "Claude CodeでFizzBuzzアプリを作ってみた"
date:   2025-10-15 22:30:00 +0900
tags: claude-code react typescript
---

> **注**: この記事はClaude Code自身に生成してもらったものです。実際の開発体験を元に、Claude Codeとの対話を通じて執筆しました。

## はじめに

AnthropicのClaude Codeを使って、ReactでFizzBuzzアプリを作成してみました。
対話形式で進めながら、技術スタックの選択から実装、GitHubへのプッシュまでを体験した記録です。

## Claude Codeとは

Claude CodeはAnthropicが提供する対話型のコーディングアシスタントCLIツールです。
自然言語で指示を出すだけで、コードの生成や編集、ファイル操作などを行ってくれます。

## 作成の流れ

### 1. 最初のリクエスト

まず、シンプルに「reactでfizzbuzzするアプリ作ってみてほしい」とお願いしました。

すると、Claude CodeはすぐにReactアプリの作成を開始。
package.json、index.html、そしてReactコンポーネントを次々と生成していきます。

### 2. TypeScriptへの変更

最初はJavaScriptで作成されていましたが、「typescriptにしてほしい」とリクエスト。

すると即座に：
- TypeScriptの型定義を追加
- tsconfig.jsonの生成
- .tsxへの拡張子変更

といった対応をしてくれました。

### 3. スタイリングの選択

CSSライブラリの選択で少し迷いました：
- 最初はCSSを提案
- 「muiでいきたい」→ Material-UIへの切り替え開始
- 「tailwindのほうが流行ってるかな」→ Tailwindの設定開始
- 「うーん。やっぱりmuiにするよ」→ 最終的にMUIで確定

この試行錯誤の過程で、Claude Codeは各リクエストに柔軟に対応してくれました。

### 4. 完成したコード

最終的に以下の技術スタックで完成：

- **React 18** - UIフレームワーク
- **TypeScript** - 型安全性
- **Material-UI (MUI)** - UIコンポーネントライブラリ
- **Vite** - ビルドツール

主要な機能：
- 1から指定した数までのFizzBuzz結果を表示
- 数字の範囲をカスタマイズ可能（1-1000）
- レスポンシブなグリッドレイアウト
- Fizz、Buzz、FizzBuzzごとに色分け表示

```typescript
interface FizzBuzzResult {
  number: number;
  value: string | number;
  type: 'fizz' | 'buzz' | 'fizzbuzz' | 'number';
}

const generateFizzBuzz = (): void => {
  const newResults: FizzBuzzResult[] = [];
  for (let i = 1; i <= count; i++) {
    if (i % 15 === 0) {
      newResults.push({ number: i, value: 'FizzBuzz', type: 'fizzbuzz' });
    } else if (i % 3 === 0) {
      newResults.push({ number: i, value: 'Fizz', type: 'fizz' });
    } else if (i % 5 === 0) {
      newResults.push({ number: i, value: 'Buzz', type: 'buzz' });
    } else {
      newResults.push({ number: i, value: i, type: 'number' });
    }
  }
  setResults(newResults);
};
```

### 5. Gitリポジトリの作成

完成したコードを「このコード、githubに」とリクエストすると：
- `~/work/claude-fizzbuzz`ディレクトリへの移動
- gitリポジトリの初期化
- .gitignoreの作成
- 初回コミットの作成

まで自動で行ってくれました。

### 6. GitHubへのプッシュ

GitHubへのプッシュは少し苦戦しました。
SSH認証の問題で、Claude CodeからはSSH agentの環境変数にアクセスできず、結局プッシュは手動で行うことに。

これは、各Bashコマンドが独立したセッションで実行されるという、Claude Codeの仕様によるものでした。

**学び**: Claude Codeは以下が得意
- ローカルでのコード作成・編集
- git commit などのローカル操作

一方、以下は手動で行う方が良い
- git push/pull/fetch（認証が必要な操作）

## 感想

### 良かった点

1. **迅速な開発** - アプリ作成から記事執筆まで、約30-40分で完了
2. **柔軟な対応** - 技術スタックの変更にも即座に対応
3. **ベストプラクティス** - TypeScriptの型定義やコンポーネント設計が適切
4. **Git統合** - リポジトリ作成からコミットまで自動化

### 改善の余地

1. **認証周り** - SSH/HTTPS認証が必要な操作は手動推奨
2. **環境変数の引き継ぎ** - Bashセッション間での環境変数共有の制限

## まとめ

Claude Codeを使うことで、アイデアから実装、リポジトリ作成までをスムーズに進めることができました。

特に、技術選択の試行錯誤にも柔軟に対応してくれる点が素晴らしかったです。
小規模なプロトタイプやサンプルアプリの作成には非常に有効なツールだと感じました。

リポジトリ: [claude-fizzbuzz](https://github.com/toyatch/claude-fizzubzz)

## 参考

- [Claude Code](https://claude.com/claude-code)
- [Material-UI](https://mui.com/)
- [Vite](https://vitejs.dev/)
