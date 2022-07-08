# docker-compose を用いた Golang サーバの実験

## 必要なツール
- docker
- docker-compose

## 起動方法

```
$ docker-compose build
$ docker-compose up
```

## 今回作成して得られた知見

- マルチステージビルドだと軽い!
    - バイト先のdocker imageが900MBであるのに対し, distroless だと20MB!!
    - もちろんサンプルアプリだからってのもあるけど
- `docker-compose.yml` には `context`を設定してあげよう!
    - でないと今回のようにDockerfileをルートに置かない構成にすることができない
    - ルートにDockerfileがボンボンおかれているのは、気分的によくない
    - ただビルドを行う際により多くのファイルがdocker deamonにおくられるから遅くなるかもとの情報があった
    - まぁそれはDockerfileをルートにおいても同じなのでは?
- `docker-compose.yml` での portの設定の仕方！
    - `{ホストのport}:{コンテナのport}` になってる！！(いつもどっちがどっちかわすれてしまう)
    - dockerは基本左側がホストのものになっているような... (e.g COPY)
- 変更した内容を反映するときにいちいち `docker-compose build && docker-compose up` するのはいやだ！
    - なんかもっといい方法あるきがする
    - golangでhot reloadにするライブラリを別のコンテナで見たことあるけど, それはそれでなぁ
    - 結局速度が気になるならホスト上のgoで動かして, きれいにするときの挙動だけコンテナで確認するとか??
    - 開発用のDockerfileを別途用意しておくとか??
        - それはそれで汚い気もする
        - まぁDockerfile自体はいくらあっても大丈夫だとは思うけど
    - Volumeをうまく活用するとか?
    - せめて `docker-compose up` したら変更分も検知してbuildしてほしいよね~

## 動作環境

念のため動作確認した環境を記録しておく.

```
$ uname -a
Darwin MY-PC-NAME 21.5.0 Darwin Kernel Version 21.5.0: Tue Apr 26 21:08:22 PDT 2022; root:xnu-8020.121.3~4/RELEASE_X86_64 x86_64

$ docker version
Client:
 Cloud integration: v1.0.24
 Version:           20.10.14
 API version:       1.41
 Go version:        go1.16.15
 Git commit:        a224086
 Built:             Thu Mar 24 01:49:20 2022
 OS/Arch:           darwin/amd64
 Context:           default
 Experimental:      true

Server: Docker Desktop 4.8.2 (79419)
 Engine:
  Version:          20.10.14
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.15
  Git commit:       87a90dc
  Built:            Thu Mar 24 01:46:14 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.5.11
  GitCommit:        3df54a852345ae127d1fa3092b95168e4a88e2f8
 runc:
  Version:          1.0.3
  GitCommit:        v1.0.3-0-gf46b6ba
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

$ docker-compose version
docker-compose version 1.29.2, build 5becea4c
docker-py version: 5.0.0
CPython version: 3.9.0
OpenSSL version: OpenSSL 1.1.1h  22 Sep 2020

$ go version
go version go1.16.4 darwin/amd64

```
