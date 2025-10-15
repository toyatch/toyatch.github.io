---
layout: post
title:  "Claude CodeでBDD形式のE2Eテストを追加してみた"
date:   2025-10-16 00:00:00 +0900
tags: claude-code cucumber playwright bdd e2e
---

> **注**: この記事もClaude Code自身に生成してもらったものです。前回のFizzBuzzアプリに、Cucumber + PlaywrightでE2Eテストを追加した過程を記録しました。

## はじめに

[前回の記事](2025-10-15-claude-code-fizzbuzz.md)でClaude Codeを使ってReact FizzBuzzアプリを作成しました。
今回は、そのアプリにBDD（振る舞い駆動開発）形式のE2Eテストを追加した記録です。

## 今回の目標

自然言語でテストを書いて、リグレッション（機能の劣化）を防止したい。

## 実装の流れ

### 1. ディレクトリ構造の再編成

まず、「ディレクトリ構造を変更したい」とリクエスト：
- `./frontend` - Reactアプリ
- `./e2e` - E2Eテスト（当初は`features`という名前を提案されましたが、`e2e`に変更）
- `./e2e/features` - .featureファイルを配置

Claude Codeが即座にディレクトリを再編成してくれました。

### 2. Cucumber + Playwrightのセットアップ

package.jsonに依存関係を追加：
```json
{
  "devDependencies": {
    "@cucumber/cucumber": "^10.0.0",
    "@playwright/test": "^1.40.0",
    "playwright": "^1.40.0",
    "ts-node": "^10.9.0",
    "typescript": "^5.3.0"
  }
}
```

設定ファイル（cucumber.js、tsconfig.json）も自動生成されました。

### 3. ユーザー価値ベースのシナリオ作成

最初、Claude Codeは技術的な観点のシナリオを提案してきました：

```gherkin
Scenario: アプリケーションが正常に表示される
  Given ユーザーがアプリケーションを開く
  Then タイトルが表示される
```

これに対して「もっとユーザー価値ベースの記載にしたい」とリクエストすると、以下のように改善：

```gherkin
Feature: FizzBuzzパターンの視覚的理解
  As a プログラミング学習者
  I want FizzBuzzの結果を視覚的に確認できる
  So that 3と5の倍数のパターンを直感的に理解できる

  Scenario: 初めてアプリを開いたときにすぐ使える
    Given 初めてアプリを訪れた学習者
    When アプリを開く
    Then すぐに使い方が分かるUIが表示される
    And デフォルトの範囲でFizzBuzzが実行できる状態になっている
```

**ポイント**:
- 「誰が」「何のために」「何を得たいか」が明確
- 技術的な詳細ではなく、ユーザーの目的に焦点

### 4. Playwrightでのステップ定義

Claude Codeが自動的にステップ定義を生成：

```typescript
Given('初めてアプリを訪れた学習者', async function (this: CustomWorld) {
  // 初回訪問者という状態
});

When('アプリを開く', async function (this: CustomWorld) {
  await this.page!.goto(APP_URL);
});

Then('すぐに使い方が分かるUIが表示される', async function (this: CustomWorld) {
  const title = await this.page!.locator('h1').textContent();
  expect(title).toContain('FizzBuzz');

  await expect(this.page!.locator('input[type="number"]')).toBeVisible();
  await expect(this.page!.locator('button:has-text("実行")')).toBeVisible();
});
```

### 5. テストの実行

依存関係をインストールして実行：

```bash
npm install
cd frontend && npm install
npm run dev  # 開発サーバー起動
npm test     # E2Eテスト実行
```

最初はPlaywrightのブラウザがインストールされていないエラーが発生：

```bash
npx playwright install chromium
```

さらにWSL環境ではシステム依存関係が不足：

```bash
sudo apt-get install libnspr4 libnss3
```

これらを解決して、再度実行すると...

```
4 scenarios (4 passed)
18 steps (18 passed)
0m07.829s
```

**全テストがパス！** ✅

### 6. 試行錯誤のポイント

#### Gherkinの言語設定

最初、`# language: ja`で日本語キーワード（「フィーチャ」「シナリオ」など）を使おうとしてエラー。
Given/When/Then/Andはそのまま使える方が良いと判断し、`# language: ja`を削除。

#### ディレクトリ命名

- 最初の提案: `features/`
- 私の提案: 「もっと適切な名前はある？」
- Claude Codeの提案: `e2e`、`tests`、`acceptance`
- 最終決定: `e2e` （最も明確）

このような対話的な改善ができるのがClaude Codeの強みです。

## 完成したディレクトリ構造

```
claude-fizzbuzz/
├── frontend/           # Reactアプリ
│   ├── src/
│   ├── package.json
│   └── ...
├── e2e/               # E2Eテスト
│   ├── features/      # Gherkinシナリオ
│   │   └── fizzbuzz.feature
│   ├── step_definitions/  # Playwrightステップ
│   │   └── fizzbuzz.steps.ts
│   └── support/       # テスト設定
│       ├── world.ts
│       └── hooks.ts
├── package.json       # E2Eテスト用
├── cucumber.js
└── tsconfig.json
```

## 実装されたシナリオ

1. **初めてアプリを開いたときにすぐ使える**
   - ユーザー価値: 学習者がすぐに使い始められる

2. **異なる範囲でパターンを確認したい**
   - ユーザー価値: カスタマイズして学習できる

3. **FizzBuzzのルールを正しく理解できる**
   - ユーザー価値: ロジックの正確性を確認できる

4. **大きな範囲でもパターンを確認できる**
   - ユーザー価値: より多くのパターンを観察できる

## 学んだこと

### 自然言語でのテスト記述

Gherkinを使うことで、非エンジニアでも読める形式でテストを記述できました。
これにより、将来の機能変更時にも「何を保証すべきか」が明確になり、リグレッション防止に役立ちます。

### Claude Codeの強み

1. **対話的改善** - 最初の提案から、フィードバックに応じて柔軟に改善
2. **文脈理解** - 「ユーザー価値ベース」という抽象的な要求を理解して具体化
3. **構造化** - ディレクトリ構成から設定ファイルまで一貫性のある構造

### 課題

1. **システム依存関係** - WSL環境でのブラウザ実行には追加パッケージが必要
2. **言語設定** - Gherkinの言語設定は英語キーワードの方がシンプル

## まとめ

Claude Codeを使うことで、約30-40分でBDD形式のE2Eテスト環境を構築できました。

自然言語でテストを記述することで、将来の保守性が大きく向上します。
機能を追加・変更する際も、既存の振る舞いを保証しながら進められるため、安心して開発を続けられます。

リポジトリ: [claude-fizzbuzz](https://github.com/toyatch/claude-fizzubzz)

## 参考

- [Cucumber](https://cucumber.io/)
- [Playwright](https://playwright.dev/)
- [前回の記事: Claude CodeでFizzBuzzアプリを作ってみた](2025-10-15-claude-code-fizzbuzz.md)
