
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