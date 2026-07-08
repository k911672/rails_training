# rails-intro

Ruby on Rails の学習用プロジェクトです。

| 項目 | バージョン |
|------|-----------|
| Ruby | 3.4.10 |
| Rails | 8.1.3 |
| DB | MySQL 8.0 |

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

## よく使うコマンド

コンテナ内では `/workspace/bin` が PATH に含まれているため、`rails` は `bin/rails` と同じコマンドとして使えます（例: `rails -v`）。

| コマンド | 説明 |
|---------|------|
| `bin/dev` | 開発サーバーを起動 |
| `rails -v` | Rails のバージョンを表示 |
| `bin/rails console` / `rails console` | Rails コンソールを起動 |
| `bin/rails test` | テストを実行 |
| `bin/ci` | CI と同じチェック一式を実行 |
| `bin/setup` | 依存関係のインストールと DB 準備（サーバー起動なし: `bin/setup --skip-server`） |
| `bin/rails db:migrate` | マイグレーションを実行 |
| `bin/rails db:seed` | シードデータを投入 |
| `bin/rails g controller users index` | Users コントローラと index アクションを生成 |

## 学習メモ

### Users コントローラの生成

次のコマンドで Users コントローラと `index` アクションを生成しました。

```bash
bin/rails g controller users index
```

主な生成・変更内容は次のとおりです。

| パス | 内容 |
|------|------|
| `app/controllers/users_controller.rb` | `UsersController`（`index` アクション） |
| `app/views/users/index.html.erb` | index 用ビュー |
| `app/helpers/users_helper.rb` | ヘルパー |
| `test/controllers/users_controller_test.rb` | コントローラテスト |
| `config/routes.rb` | `get "users/index"` が追加される |

ブラウザでは次の URL で確認できます。

```text
http://localhost:3000/users/index
```

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

## トラブルシューティング

### コンテナの再ビルド

設定変更後や環境がおかしいとき、または `rails` コマンドが使えず `bin/rails` しか効かないときは、コマンドパレットから **「Dev Containers: Rebuild Container」** を実行してください（Dockerfile の PATH 設定を反映するため）。

### DB 接続エラー

`db` サービスの起動を待ってから `app` が起動するよう設定されていますが、手動で DB を再準備したい場合は以下を実行します。

```bash
bin/rails db:prepare
```

### gem の再インストール

```bash
bundle install
```
