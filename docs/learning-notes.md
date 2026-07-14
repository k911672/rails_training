# 学習メモ

このプロジェクトで実行したコマンドと、その結果の記録です。

## Users コントローラの生成

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

## User モデルの生成

次のコマンドで User モデルと `users` テーブル用のマイグレーションを生成しました。

```bash
bin/rails g model User name:string age:integer
```

主な生成・変更内容は次のとおりです。

| パス | 内容 |
|------|------|
| `app/models/user.rb` | `User` モデル |
| `db/migrate/20260713140212_create_users.rb` | `users` テーブルを作成するマイグレーション |
| `test/models/user_test.rb` | モデルテスト |
| `test/fixtures/users.yml` | テスト用フィクスチャ |

マイグレーションで作成される `users` テーブルのカラムは次のとおりです。

| カラム | 型 |
|--------|-----|
| `name` | string |
| `age` | integer |
| `created_at` | datetime |
| `updated_at` | datetime |

マイグレーションの実行については、下記「マイグレーションの実行」を参照してください。

## マイグレーションの実行

`rails g model` でマイグレーションファイルを生成したあと、次のコマンドでデータベースにテーブルを反映しました。

```bash
rails db:migrate
```

（`bin/rails db:migrate` でも同じです）

このコマンドは、`db/migrate/` 配下の未実行マイグレーションを順に実行し、スキーマを更新します。今回は次のマイグレーションが適用されました。

| マイグレーション | 内容 |
|----------------|------|
| `20260713140212_create_users.rb` | `users` テーブルを作成 |

実行結果の確認には、次のコマンドを使います。

```bash
rails db:migrate:status
```

`Status` が `up` になっていれば、マイグレーションは適用済みです。

## マイグレーションのロールバック

マイグレーションを取り消したいときは、次のコマンドを使います。

```bash
rails db:rollback
```

（`bin/rails db:rollback` でも同じです）

直近 1 件のマイグレーションを巻き戻します。今回の `CreateUsers` マイグレーションをロールバックすると、`users` テーブルが削除されます。

複数件まとめて戻す場合は `STEP` を指定します。

```bash
rails db:rollback STEP=3
```

`STEP=3` の場合、直近 3 件のマイグレーションが取り消されます。

ロールバック後も、`rails db:migrate:status` で `Status` が `down` になっているか確認できます。再度テーブルを作り直すときは、`rails db:migrate` を実行します。

## データベースコンソール（rails dbconsole）

データベースに SQL で直接接続するには、次のコマンドを使います。

```bash
rails dbconsole
```

（`bin/rails dbconsole` でも同じです）

パスワードを聞かれたら `password` を入力します。接続できると MySQL のプロンプトが表示されます。

```text
MySQL [rails_intro_development]>
```

前提として、`app` コンテナに MySQL クライアント（`default-mysql-client`）が入っている必要があります（`docker/Dockerfile` に含まれています）。

### テーブル一覧を見る

MySQL では SQLite の `.tables` ではなく、次を使います。

```sql
SHOW TABLES;
```

マイグレーション適用後の例：

| Tables_in_rails_intro_development |
|-----------------------------------|
| `ar_internal_metadata` |
| `schema_migrations` |
| `users` |

### テーブルの構造を見る

SQLite の `.schema` に近い確認は、次で行います。

```sql
DESCRIBE users;
```

（`DESC users;` でも同じです）

カラム名・型・NULL 可否・キーなどが一覧で表示されます。CREATE 文そのものを見たい場合は次です。

```sql
SHOW CREATE TABLE users;
```

| SQLite | MySQL（このプロジェクト） |
|--------|---------------------------|
| `.tables` | `SHOW TABLES;` |
| `.schema` | `SHOW CREATE TABLE テーブル名;` / `DESCRIBE テーブル名;` |

終了するときは `exit` または `quit` を入力します。

## Rails コンソールでレコードを作成する

Active Record 経由でデータを操作するには、Rails コンソールを起動します。

```bash
rails c
```

（`rails console` / `bin/rails console` でも同じです）

コンソール内で、次のように User のレコードを作成しました。

```ruby
User.all
# => []  （最初は空）

user = User.new
user.name = 'Yuta Nakamura'
user.age = 20
user.save
# => true  （保存成功。内部では INSERT が実行される）
```

| メソッド | 意味 |
|----------|------|
| `User.all` | `users` テーブルの全件を取得 |
| `User.new` | まだ DB に保存されていない新しい User インスタンスを作る |
| `user.name =` / `user.age =` | 属性をセットする |
| `user.save` | DB に INSERT（または UPDATE）する。成功すると `true` |

`rails dbconsole` が SQL で直接操作するのに対し、`rails c` は Ruby / Active Record 経由で操作します。コンソールを終了するときは `exit` を入力します。
