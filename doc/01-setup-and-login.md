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

concourse-ciにはいくつかセットアップの方法があるが、ここではDocker Composeを利用した方法をとる。

1. Dockerのインストール
2. [github/concourse](https://github.com/concourse/concourse-docker)より、docker-composeファイルを入手する。
  
   concourseのgithubにはdocker-composeファイルが以下の2種類用意されている。
   - **docker-compose.yml** : 伝統的なマルチコンテナ構成、立ち上げには下準備が必要（後述）
   - **docker-compose-quickstart.yml** : 簡易版、下準備不要。
   
   **docker-compose.yml**を利用する場合、コンテナ同士の認証のための鍵の生成が必要となる。
   
   鍵の生成、及びそれらの配置については、[generate-keys.sh](https://github.com/concourse/concourse-docker/blob/master/generate-keys.sh)を実行することで準備可能。
   
   (Windowsの場合は、git bashや、[OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/releases)なんかを介するとできる。ただ試したところOpenSSHの方はパスフレーズなしのキー生成ができなさそう？なのでおとなしくgit bashを使うのが吉。) 
3. ```docker-compose up -d``` を実行する。

ここまでで、concourse-ciの立ち上げは完了。


# login