---
title: CircleCIでRubyのvendorをWorkflow間で共有した時の失敗談
date: 2019-10-10T19:32:15+09:00
---

## はじめに

どうもお仕事でAWS触ることが多くなっているゲインです。

最近Rubyを動かすためのAWS LambdaをTerraform + CircleCIでDeployするという構成でちょっとハマったので失敗談と解決方法の一例を示します。

## TL;DR

- それぞれのコンテナでユーザーおよび$HOMEが違う
- そのためCircleCIの `working_directory` で `~` を使うと異なるパスになることがある
- 結果的に `restore_cache` が別のパスに保存されてしまうことがある

## 前提条件

- CircleCI上でTerraformが実行されて反映される環境である。

## ハマったこと

プロジェクトの都合でRubyを使ってLambdaを動かすことになりました。

CircleCI上でのCI/CD環境が既にあったのでWorkflowを使って以下ような事を考えて、フローを追加しました。

- Terraformは既に `hashicorp/terraform` で動いているでそのままにする
- Rubyのvendorは別のjobでコンテナイメージを変更して動かす

それを実際に反映したのが以下のconfig.ymlです。

```yaml
# config.yml
version: 2
jobs:
  build: # Terraform apply
    docker:
      - image: hashicorp/terraform:0.11.14
    working_directory: ~/terraform
    environment:
      AWS_DEFAULT_REGION: ap-northeast-1
    steps:
      - checkout
      - restore_cache:
          key: source-v1-{{ .Branch }}-{{ .Revision }}-{{ checksum "lambda/src/myfunction/Gemfile.lock" }}
      - run:
          command: |
            terraform init -backend-config="bucket=${BUCKET_NAME}" -backend-config="key=terraform.tfstate" -backend-config="region=${REGION}"
            terraform apply -var-file=stg.tfvars 

  bundle_install: # bundle install for Lambda function
    docker:
      - image: circleci/ruby:2.5.0
    working_directory: ~/terraform
    steps:
      - checkout
      - run:
          name: bundle install
          command: |
            cd modules/sfn/lambda/src/myfunction
            bundle install --path vendor/bundle
      - save_cache:
          key: source-v1-{{ .Branch }}-{{ .Revision }}-{{ checksum "lambda/src/myfunction/Gemfile.lock" }}
          paths:
            - "lambda/src/myfunction/vendor"


workflows:
  version: 2
  deploy:
    jobs:
      - bundle_install
      - build:
          requires:
            - bundle_install
```

`bundle install` を実行するジョブでは `circleci/ruby:2.5.0` を使います。

実行した後のvendorはCircleCIの `save_cache` を使ってキャッシュしました。

そのキャッシュしたvendorはterraform applyを行うための `hashicorp/terraform:0.11.14` で `restore_cache` しました。

さてこれで `bundle install` でvendorが作成され、TerraformでそれらがzipにまとめられLambdaで使えるようになりましたとさ。

...とはなりませんでした。

バッチ処理のStep Function内で使われるLambdaだったので、朝起きるとCloudWatch Logsにはこんなエラーが。

```
Traceback (most recent call last):
main.rb:1:in `<main>': undefined local variable or method `GEM_NAME' for main:Object (NameError)
```

なぜかLambdaにはvendorがなく、使いたかったgemを使えないという問題でした。

## 原因

今回使っていた `circleci/ruby:2.5.0` と `hashicorm/terraform:0.11.14` ではユーザが異なっていました。

実際に手元でdockerコンテナ内に入って確認してみましょう。

まずは `hashicorp/terraform` のコンテナに入ってみます。

```
# hashicorp/terraform
docker run -it --entrypoint=ash hashicorp/terraform:0.11.14

# コンテナ内
circleci@3d233757b0cc:/$ whoami
root
circleci@3d233757b0cc:/$ echo $HOME
/root
```

ユーザーは `circleci`、 `$HOME` は `/root  とMySQLなりました。`

次に `circleci/ruby:2.5.0` に入ってみます。

```
# circleci:ruby
docker run -it circleci/ruby:2.5.0 /bin/bash

# コンテナ内
circleci@3d233757b0cc:/$ whoami
circleci

circleci@3d233757b0cc:/$ echo $HOME
/home/circleci
```

ユーザーは `circleci`、 `$HOME` は `/home/circleci  となりました。`

つまりは以下のようなことでした。

- `circleci/ruby` で  `bundle install` したときは `/home/circleci/workdir/lambda/src/vendor` のパスでキャッシュされる
- `hashicorp/terraform` のジョブでは `/root/workdir/lambda/src/vendor` のパスを期待している
- 実際には `/home/circleci/workdir/lambda/src/vendor` に実際にはキャッシュが復元される

同様の事例として、[CircleCIのサポート](https://support.circleci.com/hc/ja/articles/360004250693-%E3%82%AD%E3%83%A3%E3%83%83%E3%82%B7%E3%83%A5%E3%81%AE%E5%BE%A9%E5%85%83%E3%81%8C-Permission-Denied-%E3%81%A7%E5%A4%B1%E6%95%97%E3%81%99%E3%82%8B)にも似たような話があります。

## 解決策


上記のCircleCIのサポートページには以下の記述があります。

> 可能なら、複数のジョブ間で同じイメージを使用してください。 それが難しい場合は、2 つのイメージ間で共通の場所に作業ディレクトリを設定してください (`/tmp` が使用できることがあります)。 また、キャッシュを保存する前に、ファイルのアクセス許可を変更または設定する必要があります。

そのため `bundle install` のパスを `/tmp` 配下にして、そこから取り出すという手段をとってみました。

configは以下のようになります。

```yaml
# config.yml
version: 2
jobs:
  build: # Terraform apply
    docker:
      - image: hashicorp/terraform:0.11.14
    working_directory: ~/terraform
    environment:
      AWS_DEFAULT_REGION: ap-northeast-1
    steps:
      - checkout
      - restore_cache:
          key: source-v1-{{ .Branch }}-{{ .Revision }}-{{ checksum "lambda/src/myfunction/Gemfile.lock" }}
					
      ## ここを追加！！
      - run:
          name: Move tmp to app dir from /tmp
          command: mv /tmp/vendor lambda/src/vendor

      - run:
          command: |
            terraform init -backend-config="bucket=${BUCKET_NAME}" -backend-config="key=terraform.tfstate" -backend-config="region=${REGION}"
            terraform apply -var-file=stg.tfvars 

  bundle_install: # bundle install for Lambda function
    docker:
      - image: circleci/ruby:2.5.0
    working_directory: ~/terraform
    steps:
      - checkout
      - run:
          name: bundle install
          command: |
            cd modules/sfn/lambda/src/myfunction
            ## ここを修正！！
            bundle install --path /tmp/vendor/bundle
      - save_cache:
          key: source-v1-{{ .Branch }}-{{ .Revision }}-{{ checksum "lambda/src/myfunction/Gemfile.lock" }}
          paths:
            - "lambda/src/myfunction/vendor"


workflows:
  version: 2
  deploy:
    jobs:
      - bundle_install
      - build:
          requires:
            - bundle_install
```

ただこちらの解決策も本来はいまいちなはずなので、実際には次のような対応を取ると良いでしょう。

- circleci/rubyをterraformのジョブでも使う
- terraform自体はgoで作らているのでBuild済みのものを公式から取得して使う

## まとめ

CircleCIのworkflow間で外部ライブラリを `save_cache` で共有するときのハマったところと解決策を紹介しました。

ここまで想像力が働かずにバッチが失敗してしまったので今後は気をつけたいですね。

TerraformでLambdaを触るのは少し辛くなってきたのでなにかベストプラクティスが欲しいところです。

~~AWSさんはGlueのCralwerをLambda無しでイベントから直で動かせるようにしてください。~~


