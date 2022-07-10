---
title: Profile
date: 2019-08-11T14:31:31+09:00
---

## 基本情報

- 名前: 高野 凱
- GitHub: [gainings](https://github.com/gainings)
- Twitter: [@gainings](https://twitter.com/gainings)
- メール: gain.beats@gmail.com

## 職務経歴

|会社|在籍|
|:--:|:--:|
|[フラー株式会社](https://fuller-inc.com/career/)|アルバイト: 2017年12月15日 ~ 2019年03月31日|
|[合同会社DMM.com](https://dmm-corp.com/recruit/)| 正社員: 2019年04月01日 ~ 2022年07月10日|
|[207株式会社](https://www.notion.so/207inc/207-83c3e8da923c4120b9efb8f465526231)| 正社員: 2022年07月11日 ~ 現在|


## 職務概要
|期間|職務内容|
|----|----|
|2017年12月 ~ 2019年03月|アルバイトとして複数アプリのバックエンドをGoで開発|
|2019年04月 ~ 2019年07月 | 新卒入社後、3か月の研修で幅広く技術を勉強|
|2019年9月 ~ 2019年11月| ゲームアプリのランキング集計のためのETL処理やバッチ処理などをクラウドサービスを用いて構築 |
|2019年12月 ~ 2020年06月| オンプレからクラウドへのデータベース移行にかかる移行計画とAPI作成の支援 |
|2020年6月 ~ 2020年07月| Fargate環境のコスト最適化 |
|2020年9月 ~ 2020年10月| k8sのDocker Hub Pull Rate対策 |
|2020年9月 ~ | EKSの運用 |
|2021年6月 ~ | AWS環境の整備 IaCと監視の強化。チームへの教育 |

## 副業

### アニメファンド株式会社
[会社リンク](https://www.corp.animefund.com/)

#### 期間

2020年08月01日 ~ 2020年11月30日

#### 業務内容
クラウドファンディングサービスのインフラ面全般を担当。

以下の内容を3ヶ月程度で構築。

- AWS環境上に以下のサービスを用いた3層アーキテクチャ
  - CloudFront
  - ALB
  - Fargate(Rails + Next.js)
  - Redis
  - Aurora
- TerraformによるIaC
- AWSアカウントの整理
  - AWS Organizationを利用した組織作成
  - SSOを用いたログイン設定
- ドメインの設定
  - サブドメイン設定
  - 証明書設定
- CI/CD pipelineの設定
  - GitHubActionsとCodePipelineを利用したBlue/Greenデプロイ
- CloudWatchを利用した監視設定
- locustを利用した負荷試験のスクリプト作成と試験実行

###  207株式会社
[会社リンク](https://207-inc.com/)

#### 期間

2021年01月01日 ~ 現在

#### 業務内容

- AWS環境上に以下のサービスを用いたRailsアプリケーションおよびNode.jsアプリケーションを構築
  - CloudFront
  - ALB
  - ECS Fargate
  - Redis
  - RDS Aurora
  - S3
- RailsアプリケーションとNodeアプリケーションのDocker化
- 従来のElasticBeanstalkの環境からダウンタイムなく上記の新環境への移行を企画から準備、実行まで実施
- CI/CD pipelineの設定
  - CircleCIでのBuild
  - CodePipelineを利用したBlue/Greenデプロイ
- Terraformを使用したIaCの実現
- AWSアカウントの整理
  - AWS Organization
  - Google Workspaceを用いたAWS SSOでのログイン
  - Google Workspaceの組織内だけがアクセス可能なOAuthの設定
- ドメインの設定
  - 散財していたドメインをAWSのRoute53に統合
  - サブドメイン設定
  - 証明書設定
- NewRelicを利用した監視設定
- データ分析基盤の整備
  - Auroraにあるデータを定期的にマスクし、S3に吐き出しAthena経由でRedashからアクセスするためのETL環境整備
  - RedashをECS on EC2で構築
  - RedashのログインをOAuthで可能に
- SESでのメール送信の設定
- コンテナの脆弱性診断の通知
- メンテ等の保守運用作業

## 技術経験

|名前|レベル|
|----|----|
| Go | 一人でAPIの設計が可能|
| AWS |一般的なアーキテクチャをIaCで構築可能|

## 執筆

|記事|
|----|
|[re:Invent 2019参加レポート](https://inside.dmm.com/entry/2019/12/26/aws-reinvent2019)|

## 取材

|記事|
|----|
|[AWS トレーニング活用事例：DMM.com](https://aws.amazon.com/jp/training/case-studies/dmm-training/)|

## 保有資格

|取得日|資格名|
|----|----|
|2019年06月29日|[AWS認定ソリューションアーキテクト - アソシエイト](https://www.youracclaim.com/badges/50048e72-32e2-4fc6-9823-9d0993e99fc6/public_url)|
|2019年10月19日|[AWS認定デベロッパー - アソシエイト](https://www.youracclaim.com/badges/0c2ea115-7c2e-4828-9ef6-e26146669452/public_url)|
|2021年3月7日|[Certified Kubernetes Application Developer](https://www.youracclaim.com/badges/09e568a2-b1c8-46b2-907f-120891ec9fd6/public_url)|

