# よく使うコマンド

コンテナ内では `/workspace/bin` が PATH に含まれているため、`rails` は `bin/rails` と同じコマンドとして使えます（例: `rails -v`）。

| コマンド | 説明 |
|---------|------|
| `bin/dev` | 開発サーバーを起動 |
| `rails -v` | Rails のバージョンを表示 |
| `bin/rails console` / `rails console` / `rails c` | Rails コンソールを起動 |
| `bin/rails dbconsole` / `rails dbconsole` | MySQL クライアントを起動（DB に直接接続） |
| `bin/rails test` | テストを実行 |
| `bin/ci` | CI と同じチェック一式を実行 |
| `bin/setup` | 依存関係のインストールと DB 準備（サーバー起動なし: `bin/setup --skip-server`） |
| `bin/rails db:migrate` / `rails db:migrate` | 未実行のマイグレーションをデータベースに反映 |
| `bin/rails db:migrate:status` / `rails db:migrate:status` | マイグレーションの適用状況を確認 |
| `bin/rails db:rollback` / `rails db:rollback` | 直近のマイグレーションを取り消す |
| `bin/rails db:seed` | シードデータを投入 |
| `bin/rails g controller users index` | Users コントローラと index アクションを生成 |
| `bin/rails g model User name:string age:integer` | User モデルとマイグレーションを生成 |

各コマンドの詳細な学習メモは [learning-notes.md](./learning-notes.md) を参照してください。
