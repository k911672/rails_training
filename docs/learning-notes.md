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

`new` のときに属性をまとめて渡すこともできます。

```ruby
liam = User.new(name: 'Liam', age: 20)
liam.save

olivia = User.new(name: 'Orivia', age: 21)
olivia.save

lucas = User.new(name: 'Lucas', age: 24)
lucas.save

User.all
# => [#<User id: 3, name: "Liam", ...>, #<User id: 4, name: "Orivia", ...>, #<User id: 5, name: "Lucas", ...>]
```

どちらの書き方も結果は同じで、`save` 時に INSERT されます。id は 1, 2 を削除済みのため、この例では 3 から採番されています。

| メソッド | 意味 |
|----------|------|
| `User.all` | `users` テーブルの全件を取得 |
| `User.new` | まだ DB に保存されていない新しい User インスタンスを作る |
| `User.new(name: ..., age: ...)` | 属性をまとめてセットした新しいインスタンスを作る |
| `user.name =` / `user.age =` | 属性をセットする |
| `user.save` | DB に INSERT（または UPDATE）する。成功すると `true` |
| `User.find(id)` | 主キー（`id`）で 1 件を取得。見つからないと例外 |
| `user.destroy` | レコードを DB から削除する（DELETE） |

### id でレコードを取得する

保存済みのレコードは、主キーで取得できます。

```ruby
`User.find(2)`
# => #<User id: 2, name: "Taro Yamada", age: 24, ...>
```

内部では次のような SQL が実行されます。

```sql
SELECT `users`.* FROM `users` WHERE `users`.`id` = 2 LIMIT 1
```

### レコードを更新する

取得したレコードの属性を変更して `save` すると、UPDATE されます。

```ruby
user = User.find(2)
user.name = 'Hanako Yamada'
user.age = 30
user.save
# => true  （更新成功。内部では UPDATE が実行される）

User.find(2)
# => #<User id: 2, name: "Hanako Yamada", age: 30, ...>
```

新規作成時の `save` は INSERT、既存レコードの `save` は UPDATE になります。例として発行される SQL は次のとおりです。

```sql
UPDATE `users`
SET `users`.`name` = 'Hanako Yamada',
    `users`.`age` = 30,
    `users`.`updated_at` = '...'
WHERE `users`.`id` = 2
```

`updated_at` は Rails が自動で更新します。`created_at` は変わりません。

### レコードを削除する

取得したレコードに対して `destroy` を呼ぶと、DB から削除されます。

```ruby
user = User.find(2)
user.destroy
# => #<User id: 2, ...>  （削除されたオブジェクト自体は返り値として残る）

User.find(2)
# => ActiveRecord::RecordNotFound  （DB 上にもう存在しない）
```

内部では次のような SQL が実行されます。

```sql
DELETE FROM `users` WHERE `users`.`id` = 2
```

削除後に同じ `id` で `find` すると `ActiveRecord::RecordNotFound` になります。存在チェックだけしたい場合は `User.find_by(id: 2)`（見つからなければ `nil`）を使います。

変数に入れずに、1 行で取得と削除をまとめることもできます。

```ruby
User.find(1).destroy

User.all
# => []  （全件削除後）
```

`rails dbconsole` が SQL で直接操作するのに対し、`rails c` は Ruby / Active Record 経由で操作します。コンソールを終了するときは `exit` を入力します。

## コントローラとビューでデータを渡す

ビュー（`app/views/users/index.html.erb`）では、コントローラでセットしたインスタンス変数を使えます。

```ruby
# app/controllers/users_controller.rb
class UsersController < ApplicationController
  def index
    @num = 10 + 1
    @users = User.all
  end
end
```

```erb
<%# app/views/users/index.html.erb %>
<p><%= @num %></p>
<ul>
<% @users.each do |user| %>
  <li>
    <%= user.id %>, <%= user.name %>, <%= user.age %>
  </li>
<% end %>
</ul>
```

| 記法 | 意味 |
|------|------|
| `<%= ... %>` | 式の結果を HTML に出力する |
| `<% ... %>` | 式を実行するだけで出力しない（`each` など） |
| `@users = User.all` | コントローラで全ユーザを取得し、ビューに渡す |

### `undefined method 'each' for nil` が起きるとき

ビューで `@users.each` しているのに、コントローラで `@users` をセットしていないと、`@users` は `nil` になり次のエラーになります。

```text
undefined method 'each' for nil
```

対処は、コントローラの `index` で `@users = User.all` などを代入することです。ビューで使う `@` 変数は、コントローラ側で先に用意する必要があります。
