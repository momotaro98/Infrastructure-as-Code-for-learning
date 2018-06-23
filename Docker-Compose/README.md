# Docker Compose Overview

```
$ tree
.
├── Dockerfile
├── app.py
├── docker-compose.yml
├── requirements.txt
├── static
│   ├── css
│   │   └── bootstrap.css
│   └── images
│       ├── docker-machine-01.jpg
│       ├── docker-machine-02.jpg
│       ├── docker-machine-03.jpg
│       ├── docker-machine-04.jpg
│       └── docker-machine-05.jpg
└── templates
    └── index.html
```

docker-compose.yml

```
version: '3.3'
services:
  # WebServer config
  webserver:
    build: .
    ports:
     - "80:80"
     depends_on:
       - redis

  # Redis config
  redis:
    image: redis
```

Dockerfile

```
# Base Image
FROM python:3.6

# Maintainer
LABEL maintainer "Shiho ASA"

# Upgrade pip
RUN pip install --upgrade pip

# Install Path
ENV APP_PATH /opt/imageview

# Install Python modules needed by the Python app
COPY requirements.txt $APP_PATH/
RUN pip install --no-cache-dir -r $APP_PATH/requirements.txt

# Copy files required for the app to run
COPY app.py $APP_PATH/
COPY templates/ $APP_PATH/templates/
COPY static/ $APP_PATH/static/

# Port number the container should expose
EXPOSE 80

# Run the application
CMD ["python", "/opt/imageview/app.py"]
```

## 複数のDockerコンテナの起動

```
$ docker-compose up
```

## Docker Composeで稼働しているコンテナ状態の確認

```
$ docker-compose ps
```

## 複数のDockerコンテナの停止 => リソースの削除

```
$ docker-compose stop
```

```
$ docker-compose down
```

# docker-compose.yml

## イメージの指定(image)

> Dockerコンテナのもとになる、ベースイメージを指定するには、imageを使います。
> イメージのタグを指定しない場合、最新バージョン(latest)がダウンロードされます。プロダクション環境で利用するときは、タグを指定してバージョンを固定しておきましょう。

```
services:
webserver:
  image: ubuntu
```

## イメージのビルド(build)

> 任意の名前のDockerfileをビルドするときは、「dockerfile」を指定します。その際、DockerfileがあるディレクトリのパスやGitレポジトリのURLを「context」で指定します。以下の例では「/data」に格納されている「Dockerfile-alternate」という名前のDockerfileをビルドしています。なお、ファイルパスは相対パスでも指定できます。

```
services:
  webserver:
    build:
      context: /data
      dockerfile: Dockerfile-alternate
```

> また、Dockerイメージをビルドする際に引数をargsとして指定できます。

```
services:
  webserver:
    build:
      args:
        projectno: 1
        user: asa
```

## コンテナ内で動かすコマンドの指定 (Command / entrypoint)

```
services:
  webserver:
    command: /bin/bash
    entrypoint:
        - php
        - -d
        - memory_limit=-1
```

## コンテナ間の連携(links)

> 別のコンテナへリンク機能を使って連携したいときは、linksに連携先のコンテナ名を設定します。たとえば、logserverという名前のコンテナとリンクさせたいときは、下記のように指定します。また、コンテナ名とは別にエイリアス名を付けたいときは、「サービス名:エイリアス名」を設定します。

```
services:
  webserver:
    links:
      - logserver
      - logserver:log01
```

> また、サービス間の依存関係はdepends_onを使って指定できます。depends_onはサービスを起動する順番も指定できます。

## コンテナ間の通信 (ports / expose)

```
services:
  webserver:
    ports:
      - "3000"
      - "8000:8000"
      - "49100:22"
      - "127.0.0.1:8001:8001"
```

> ホストマシンへポートを公開せず、リンク機能を使って連携するコンテナにのみポートを公開するとき、exposeを指定します。
> ログサーバなどように、ホストマシンから直接アクセスせず、Webアプリケーションサーバ機能を持つコンテナを介してのみアクセスを行いたい場合などに使います。

```
expose:
  - "3000"
  - "8000"
```

## サービスの依存関係の定義 (depends_on)

> 複数のサービスの依存関係を定義するときは、depends_onを指定します。例えば、webserverコンテナを開始する前にdbコンテナとredisコンテナを開始したいときは、以下のように定義します。

```
services:
  webserver:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```

> ここで注意しておきたいのは、depends_onがコンテナの開始の順序を制御するだけで、コンテナ上のアプリケーションが利用可能になるまで待つという制御を行わない点です。つまり、依存関係にあるデータベースの準備が整うまで待つわけではないため、アプリケーション側で対策する必要があります。

## コンテナの環境変数の指定 (environment / env_file)

```
services:
  webserver:
    environment:
      - HOGE=fuga
    env_file:
      - ./envfile1
      - ./app/envfile2
      - /tmp/envfile3
```

## コンテナのデータ管理 ボリュームマウント (valumes / volumes_from)

> コンテナにボリュームをマウントするときは、volumesを指定します。下記の例では、`/var/lib/mysql`をマウントしています。さらに、ホスト側でマウントするパスを指定するには、ホストのディレクトリパス:コンテナのディレクトリパスを指定します。

```
services:
  db:
    - /var/lib/mysql
    - cache/:/tmp/cache
```

> 別のコンテナからすべてのボリュームをマウントするときは、volumes_fromにコンテナ名を指定します。たとえば、logという名前のコンテナにマウントするときは、下記のように指定します。

```
services:
  webserver:
    volumes_from:
      - log
```
