---
layout: post
title:  "Docker ComposeでWordPress環境を構築してみた"
date:   2025-10-16 21:30:00 +0900
tags: docker docker-compose wordpress mysql arm64
---

> **注**: この記事もClaude Code自身に生成してもらったものです。Docker ComposeでWordPressとMySQLの開発環境を構築した過程を記録しました。

## はじめに

WordPressのローカル開発環境をDocker Composeで立ち上げてみました。
ARM64環境（WSL2上）での構築だったため、docker-composeのインストールから必要でしたが、全体的にスムーズに進められました。

## 今回の目標

- Docker ComposeでWordPress + MySQLの環境を構築
- 環境変数を使った設定の管理
- Gitで管理できる形にする

## 実装の流れ

### 1. プロジェクトディレクトリの作成

まず、専用のディレクトリを作成：

```bash
mkdir -p ~/work/my-wordpress
cd ~/work/my-wordpress
```

### 2. docker-compose.ymlの作成

WordPress用のコンテナとMySQL用のコンテナを定義：

```yaml
version: '3.8'

services:
  db:
    image: mysql:8.0
    container_name: wordpress_mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - wordpress_network

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    container_name: wordpress_app
    restart: always
    ports:
      - "${WORDPRESS_PORT:-8080}:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: ${MYSQL_USER}
      WORDPRESS_DB_PASSWORD: ${MYSQL_PASSWORD}
      WORDPRESS_DB_NAME: ${MYSQL_DATABASE}
    volumes:
      - wordpress_data:/var/www/html
    networks:
      - wordpress_network

volumes:
  db_data:
  wordpress_data:

networks:
  wordpress_network:
    driver: bridge
```

**ポイント**:
- 環境変数を使って機密情報を分離
- 名前付きボリュームでデータを永続化
- カスタムネットワークでコンテナ間通信を確保

### 3. 環境変数の設定

`.env`ファイルで認証情報を管理：

```env
# MySQL Configuration
MYSQL_ROOT_PASSWORD=rootpassword
MYSQL_DATABASE=wordpress
MYSQL_USER=wordpress_user
MYSQL_PASSWORD=wordpress_password

# WordPress Configuration
WORDPRESS_PORT=8080
```

### 4. .gitignoreの設定

機密情報を含む`.env`をGit管理から除外：

```
.env
```

代わりに`.env.example`をテンプレートとして用意：

```env
# MySQL Configuration
MYSQL_ROOT_PASSWORD=rootpassword
MYSQL_DATABASE=wordpress
MYSQL_USER=wordpress_user
MYSQL_PASSWORD=wordpress_password

# WordPress Configuration
WORDPRESS_PORT=8080
```

### 5. Gitリポジトリの初期化

```bash
git init
git add docker-compose.yml .env.example .gitignore
git commit -m "Initial commit: Add WordPress with MySQL setup using Docker Compose"
```

**注意点**:
最初、親ディレクトリ（`~/work`）がすでにGitリポジトリだったため、そこにコミットされてしまいました。
`my-wordpress`ディレクトリを独立したリポジトリにするため、親ディレクトリの`.git`を削除してから、改めて`my-wordpress`内でGitを初期化しました。

### 6. docker-composeのインストール（ARM64環境）

ARM64環境（WSL2）では、docker-composeが未インストールだったため、以下の手順でインストール：

```bash
# 最新版をダウンロード
curl -SL https://github.com/docker/compose/releases/latest/download/docker-compose-linux-aarch64 -o /tmp/docker-compose

# インストール
sudo mv /tmp/docker-compose /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# バージョン確認
docker-compose --version
# Docker Compose version v2.40.0
```

**ポイント**:
- ARM64用のバイナリを指定（`docker-compose-linux-aarch64`）
- 最新版が自動的に取得される

### 7. WordPress環境の起動

```bash
docker-compose up -d
```

ブラウザで`http://localhost:8080`にアクセスすると、WordPressのセットアップ画面が表示されます。

## 完成したディレクトリ構造

```
my-wordpress/
├── docker-compose.yml   # コンテナ定義
├── .env                 # 環境変数（Git管理外）
├── .env.example         # 環境変数テンプレート
└── .gitignore          # Git除外設定
```

## よく使うコマンド

```bash
# 起動
docker-compose up -d

# 停止
docker-compose down

# ログ確認
docker-compose logs -f

# コンテナ状態確認
docker-compose ps

# データを含めて完全削除
docker-compose down -v
```

## 学んだこと

### 環境変数による設定管理

`.env`ファイルを使うことで：
- 機密情報をコードから分離
- 環境ごとに異なる設定を簡単に管理
- `.env.example`でテンプレートを共有

### ARM64環境での対応

ARM64環境では：
- docker-composeのインストールが必要な場合がある
- アーキテクチャに合ったバイナリを選択する必要がある
- 公式のDockerイメージ（wordpress、mysql）はARM64にも対応済み

### Gitリポジトリの管理

- 親ディレクトリがGitリポジトリの場合、意図せずそこにコミットされる可能性がある
- プロジェクトごとに独立したリポジトリを持つ場合は、ディレクトリ構造を事前に確認

## まとめ

Claude Codeを使うことで、約10分でWordPress開発環境を構築できました。

Docker Composeを使うメリット：
1. **ポータビリティ** - 環境を簡単に再現できる
2. **分離** - ホスト環境を汚さない
3. **バージョン管理** - 設定ファイルをGitで管理できる
4. **スケーラビリティ** - 必要に応じてコンテナを追加できる

WordPressのローカル開発環境として、非常に実用的な構成になりました。

## 参考

- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [WordPress Docker Image](https://hub.docker.com/_/wordpress)
- [MySQL Docker Image](https://hub.docker.com/_/mysql)
