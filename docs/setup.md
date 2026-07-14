# セットアップ

## 必要な環境（Dev Container 利用時）

Dev Container で開発する場合、ローカルに Ruby や MySQL をインストールする必要はありません。以下だけ用意してください。

- [Docker Desktop](https://www.docker.com/products/docker-desktop/)（起動済みであること）
- [Cursor](https://cursor.com/) または [Visual Studio Code](https://code.visualstudio.com/)
- [Dev Containers 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)（Cursor では標準で利用可能な場合があります）

## Dev Container の起動方法

1. このリポジトリをクローンして Cursor / VS Code で開く
2. コマンドパレット（`Cmd + Shift + P` / `Ctrl + Shift + P`）を開く
3. **「Dev Containers: Reopen in Container」** を実行する
4. 初回は Docker イメージのビルドとコンテナの作成に数分かかります

コンテナが起動すると、ターミナルは自動的に `app` コンテナ内（`/workspace`）に接続されます。

### 初回起動時に自動で行われること

`postCreateCommand` により、以下が自動実行されます。

```bash
bundle install && bin/rails db:prepare
```

- gem のインストール（`vendor/bundle` に保存）
- データベースの作成とマイグレーション

エラーなく完了していれば、追加の初期セットアップは不要です。

## 開発サーバーの起動（localhost:3000）

Dev Container を開いただけでは Rails サーバーは起動しません。`http://localhost:3000` を使うには、コンテナ内のターミナルで次を実行してください。

```bash
bin/dev
```

（`rails server` でも同じです）

起動ログに Listening などが出たら、ブラウザで [http://localhost:3000](http://localhost:3000) を開きます。ポート `3000` は Dev Container から自動的にフォワードされます。

止めるときは、そのターミナルで `Ctrl+C` を押します。

## 構成

Dev Container は `docker-compose.yml` を使い、以下の 2 サービスで構成されています。

| サービス | 役割 |
|---------|------|
| `app` | Ruby 3.4 + Rails アプリケーション |
| `db` | MySQL 8.0 |

### データベース接続情報（開発環境）

| 項目 | 値 |
|------|-----|
| ホスト | `db`（コンテナ内）/ `localhost`（ホストから） |
| ポート | `3306` |
| ユーザー名 | `root` |
| パスワード | `password` |
| データベース名 | `rails_intro_development` |
