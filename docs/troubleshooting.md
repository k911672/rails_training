# トラブルシューティング

## コンテナの再ビルド

設定変更後や環境がおかしいとき、または `rails` コマンドが使えず `bin/rails` しか効かないときは、コマンドパレットから **「Dev Containers: Rebuild Container」** を実行してください（Dockerfile の PATH 設定を反映するため）。

## DB 接続エラー

`db` サービスの起動を待ってから `app` が起動するよう設定されていますが、手動で DB を再準備したい場合は以下を実行します。

```bash
bin/rails db:prepare
```

## gem の再インストール

```bash
bundle install
```

## ポート 3000 が既に使われている

Dev Container 起動時に `Bind for 0.0.0.0:3000 failed: port is already allocated` と出る場合、ホスト側で既に別プロセスが 3000 番を使っています。使っているプロセスを止めるか、Docker 上の古いコンテナを整理してから再起動してください。

## rails dbconsole で mysql が見つからない

`Couldn't find database client: mysql, mysql5` と出る場合、`app` コンテナに MySQL クライアントが入っていません。`docker/Dockerfile` に `default-mysql-client` が含まれていることを確認し、**Rebuild Container** してください。
