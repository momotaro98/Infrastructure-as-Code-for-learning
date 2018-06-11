
# Docker Commands

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

##  環境変数の一括設定

```
# 環境変数を定義したファイルenv_listファイルを用意
$ cat env_list
hoge=fuga
foo=bar

$ docker container run -it --env-file=env_list centos /bin/bash
```

## 作業ディレクトリの設定

```
$ docker container run -it -w=/tensorflow centos /bin/bash

[root@aaaaaa work]# pwd
/tensorflow 
```

## 稼働コンテナの一覧表示

```
docker container ls -a
```

## コンテナの稼働確認

```
docker container stats [コンテナ識別子]
```

## ある1コンテナ内のプロセス確認

```
docker container tops [コンテナ識別子]
```

## 停止しているコンテナを起動

```
docker container start コンテナ識別子
```

## コンテナの停止

```
docker container stop コンテナ識別子
```

## コンテナの再起動

```
docker container restart コンテナ識別子
```

## 停止しているコンテナを削除

```
docker container rm コンテナ識別子
```

## コンテナの中断/再開

```
# 中断
$ docker container pause コンテナ識別子
# 再開
$ docker container unpause コンテナ識別子
```

## Dockerネットワークの一覧表示

```
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
06a1f1fb24d5        bridge              bridge              local
afacc6da0fe8        host                host                local
fc62fbb53c0b        none                null                local
```

## 新しいネットワークの作成

```
$ docker network create --driver=bridge web-network
```

## ネットワークへの接続

```
$ docker network connect web-network webfront
```
上記コマンドを実行すると「webfront」という名前のDockerコンテナを「web-network」という名前のDockerネットワークに接続する。接続後は、同一ネットワーク上にある他のコンテナと通信可能になる。コンテナの接続はIPアドレスだけでなくコンテナ名またはコンテナIDもそもまま使える。

コンテナのネットワーク確認
```
$ docker container inspect webfront
```

## ネットワークからの切断

```
$ docker network disconnect web-network webfront
```

## ネットワークの詳細確認

```
$ docker network inspect web-network
```

## ネットワークの削除

```
$ docker network rm web-network
```

## 稼働中コンテナへの接続

```
$ docker container attach sample

[root@aaaaaaa /]# ← ここで[Ctrl]+[P] [Ctrl]+[Q]を入力

$ ← コンテナは起動したまま/bin/bashのみ削除
$ docker container ls 
```

## 稼働コンテナでプロセス

```
docker container exec [オプション] コンテナ識別子 実行するコマンド
```

```
$ docker container exec -it webserver /bin/echo "Hello world"
```

## 稼働コンテナのポート転送確認

```
$ docker container port webserver
80/tcp -> 0.0.0.0:80
```

## コンテナからホストへのファイルコピー

```
$ docker container cp webserver:/etc/nginx/nginx.conf /tmp/nginx.conf
```

## ホストからコンテナへのファイルコピー

```
$ docker container cp ./test.txt webserver:/tmp/test.txt
```

## コンテナからイメージを作成

```
$ docker container commit -a "ASA SHIHO" webserver asashiho/webfront:1.0
```

上記はwebserverという名前のコンテナをasashiho/webfrontという名前のイメージ名でタグ名を1.0に指定してイメージを作成する。

## コンテナをtarファイル出力

```
$ docker container export webserver > latest.tar
```

## Linux OSイメージディレクトリからDockerイメージ作成

```
docker image import ファイルまたはURL - [イメージ名[:タグ名]]
```

## Dockerイメージをtarファイルに保存

```
docker image save [オプション] 保存ファイル名 [イメージ名]
```

## tarファイルからイメージを生成

```
docker image load -i export.tar
```

> `docker container export`コマンドで作成したものを読み込むときは`docker image import`コマンド、`docker image save`コマンドで生成したものを読み込むときは`docker image load`コマンドを使うこと

## 不要なイメージ/コンテナを一括削除

docker system pruneコマンドを使うと、使用していないイメージ・コンテナ・ボリューム・ネットワークを一括で削除できる。

```
docker system prune [オプション]
```

# Dockerfile

Dockerfileでは以下の情報を記述する

* ベースになるDockerイメージ
* Dockerコンテナ内で行った捜査(コマンド)
* 環境変数などの設定
* Dockerコンテナ内で動作させておくデーモン実行


| 命令  | 説明  |
|---|---|
| FROM  | ベースイメージの指定  |
| RUN  |  コマンド実行  |
