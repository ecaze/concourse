# concourse ci setup and login

- [introduction](#introduction)
- [setup](#setup)
- [login](#login)

# introduction

concourse-ciは以下の特徴をもつCI/CDプロダクトである。

- **Configuration As Code**
    - 設定はすべてコード(YAML)によって定義される。
    - そのため、同一のコードであればどの実行環境においても同じ結果が期待できる。
- **操作は原則CLI上で行う**
    - Webインターフェースも用意されているが基本的に操作はCLI上で行う
    - CLI上の操作では**fly**と呼ばれるCLIツールを利用する。
- **すべての処理はコンテナ上で行われる**
    - concourse-ciは処理を行う際、必ずコンテナを立ち上げた上で実行する。そのため、すべての処理は常にきれいな環境で実行される。
    - 処理を行う際のコンテナ自体もユーザが指定可能。そこの含めての設定である。
    
# setup

## setup concourse

concourse-ciにはいくつかセットアップの方法があるが、ここではDocker Composeを利用した方法をとる。

1. Dockerのインストール
2. [github/concourse](https://github.com/concourse/concourse-docker)より、docker-composeファイルを入手する。
  
   concourseのgithubにはdocker-composeファイルが以下の2種類用意されている。
   - **docker-compose.yml** : 伝統的なマルチコンテナ構成、立ち上げには下準備が必要（後述）
   - **docker-compose-quickstart.yml** : 簡易版、下準備不要。
   
   **docker-compose.yml**を利用する場合、コンテナ同士の認証のための鍵の生成が必要となる。
   
   鍵の生成、及びそれらの配置については、[generate-keys.sh](https://github.com/concourse/concourse-docker/blob/master/generate-keys.sh)を実行することで準備可能。
   
   (Windowsの場合は、git bashや、[OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/releases)を介することで実行可能。ただ試したところOpenSSHの方はパスフレーズなしのキー生成ができなさそう？なのでおとなしくgit bashを使うのが吉。) 
3. `docker-compose up -d` を実行する。

これで、concourse-ciの立ち上げは完了。

## setup ci tool

次に、concourse-ciをCLI上で扱うためのツールをセットアップする。

1. [http://127.0.0.1:8080](http://127.0.0.1:8080)をブラウザで開く
2. 利用しているOSに合わせて、**fly(CLI Tool)** をダウンロードする。
3. パスの通っているフォルダに`fly`バイナリを配置する or `fly`バイナリがあるフォルダにパスを通す。

これで、concourse-ciをCLI上から扱う`fly`の導入が完了。

きちんと`fly`が実行可能かどうか、`fly --version`などを実行することで確認しておくこと。

# login

次に立ち上がったconcourse-ciにログインする。

concourse-ciを利用する上で、Webインターフェース上はもとより、CLIツールにおいてもログイン処理が必要となる。

## Web インターフェース

1. [http://127.0.0.1:8080](http://127.0.0.1:8080)をブラウザで開く
2. 右上のloginをクリックする。
3. username/passwordを入力する（今回利用しているdocker-composeファイルではtest/testユーザが設定されている）

> **memo: 立ち上げ時のログイン動作**
>
> 立ち上げたばかりの状態でログインを行うと時たま400エラーでログインできない場合がある。
>
> パイプラインをセットすると改善したりするので起きた場合はひとまずそれで回避。

## CLI

1. コマンドラインから `fly --target concourse-memo login --concourse-url http://127.0.0.1:8080 -u test -p test`と実行する。
    
    上記コマンドは `fly -t concourse-memo login -c http://127.0.0.1:8080 -u test -p test`というショートハンドでも実行可能。
    
CLI上で操作する場合、自身が今ログイン済みかどうかを確認したい場合がある。

その場合は、 `fly -t concourse-memo status`を実行するとログイン済みかどうか確認できる。