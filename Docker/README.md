
# Docker

## Docker Info

```
docker version
docker system info
docker system df -v
docker image ls
```

## Nginx イメージをPull

```
docker pull nginx
```

## Dockerイメージ「nginx」を使って「webserver」という名前のDockerコンテナを起動する

```
docker container run --name webserver -d -p 80:80 nginx
```

-p ホストポート番号:コンテナポート番号
=> ポートを待ち受ける

## Nginxのサーバの状態を確認

```
docker container ps
```

## コンテナ稼働確認

```
docker container stats webserver
```


## コンテナのバックグラウンド実行

```
docker container run -d centos /bin/ping_localhost
```

“-d”は”detach”の意味でコンテナをバックグラウンドで実行する ← 逆にこれをつけないとコマンドを実行し終わったらコンテナは終了する

## コンテナのバックグラウンド実行状態確認

```
docker container logs -t fbcdab0e9417
```

fbcdab0e9417はコンテナ識別子 (container id)

## ディレクトリの共有

```
docker container run -v /Users/asa/webap:/usr/share/nginx/html nginx
```

-v (= --volume )オプション
↑ ホストの/Users/asa/webap ディレクトリをコンテナの/usr/share/nginx/html と共有する

## docker container run コマンドの環境設定

```
docker container run -it -e foo=bar centos /bin/bash
```

-eオプション(--env )で環境変数を設定できる