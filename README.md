
# Docker Composeを使用してインストールする

## Setup EC4 source code

```sh
git clone YOUR_REPO ec4-source
```

## Original document: https://doc4.ec-cube.net/quickstart_install

### 前提として、 Docker Desktop のインストール が必要です。
* 初期状態では SQLite3 を使用します
* ローカルディレクトリをマウントします

```sh
# コンテナの起動 (初回のみビルド処理あり)
docker-compose up -d

# 初回はインストールスクリプトを実行( **`www-data` ユーザで実行する点に注意！** )
docker-compose exec -u www-data ec-cube bin/console eccube:install
```

### 2回目以降の起動時も同様のコマンドを使用します。

```sh
# コンテナの起動
docker-compose up -d

# コンテナの停止
docker-compose down
```

## 各種コンテナの使用

C-CUBE 4が動作するWebサーバを含め、以下のコンテナが簡単に起動できます。
||||
|---|---|---|
|ec-cube|	EC-CUBE 向けPHP Webサーバ	|http://localhost:8080|
|postgres|	PostgreSQLデータベースサーバ||
|mysql|	MySQLデータベースサーバ||
|mailcatcher|	MailCatcher デバッグ用SMTPサーバ|http://localhost:1080|

起動時にコンテナ名を列挙することで、各種コンテナを起動します。
```sh
# 例：EC-CUBEとMySQLとphpMyAdminとMailCatcherを起動する
docker-compose up -d ec-cube mysql mailcatcher

# 省略した場合はすべてのサービスが起動します
docker-compose up -d
```
## メール送信を使用する場合

`.env` にて `MAILER_URL=smtp://mailcatcher:1025` としておきます。

## MySQL を使用する場合

`.env` にて `DATABASE_URL=mysql://dbuser:secret@mysql/eccubedb` としておきます。

データベーススキーマを初期化していない場合は、以下の実行が必要です。
```sh
# スキーマ作成+初期データ投入
docker-compose exec ec-cube composer run-script compile
```

## Apache での設定例

httpd.conf や、 .htaccess ファイルに設定します。
```
SetEnv APP_ENV prod
SetEnv APP_DEBUG 0
SetEnv DATABASE_URL pgsql://dbuser:password@127.0.0.1/cube4_dev
SetEnv DATABASE_SERVER_VERSION 10.5
SetEnv ECCUBE_AUTH_MAGIC 8PPlCHZVdH5vbMkIUKeuTeDHycQQMuaB
SetEnv ECCUBE_ADMIN_ALLOW_HOSTS []
SetEnv ECCUBE_FORCE_SSL false
SetEnv ECCUBE_ADMIN_ROUTE admin
SetEnv ECCUBE_COOKIE_PATH /
```