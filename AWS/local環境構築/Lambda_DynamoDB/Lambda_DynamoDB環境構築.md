# ローカルで Lambda と DynamoDB を使えるようにする

- 結論：なくてもいいかもしれない
  [参考](https://qiita.com/nahiro_tus/items/b6d0ae6a554a877851da)

<details><subject>ローカルDynamoDB</subject>

## Java ランタイム環境 (JRE) バージョン 17.x 以降をインストール

## DynamoDB ローカルのダウンロード

[DynamoDB ローカルダウンロード](https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html)

- 5 番まで実行し疎通確認できたら一旦 ctrl C で抜けて OK

</details>

<details><subject>serverlessの利用</subject>

## サンプルのダウンロード

[clone して利用する](https://github.com/Nalagami/serverless-dynamodb-local-sample)

## プラグインインストール

```sh
serverless plugin install -n serverless-dynamodb
serverless plugin install -n serverless-python-requirements
serverless plugin install -n serverless-offline
```

- ここまでは多分うまくいく

- ローカルの DynamoDB のテーブル情報取得

```sh
aws dynamodb list-tables --endpoint-url http://localhost:8000
```

- aws コマンド使えるようにしておく必要あり
- ちなみにその中の情報みたい場合はこれ

```sh
aws dynamodb scan --table-name Movies-dev --endpoint-url http://localhost:8000
```

- これ以降をやる前に clone したフォルダの serverless.environment に環境情報追記

```yaml
DYNAMODB_TABLE_NAME: Movies-dev
IS_OFFLINE: true
```

- こうなっていれば OK

```yaml
provider:
  name: aws
  stage: ${self:custom.stage}
  region: ap-northeast-1
  runtime: python3.10
  environment:
    TZ: Asia/Tokyo
    DYNAMODB_TABLE_NAME: Movies-dev
    IS_OFFLINE: true
```

```sh
serverless dynamodb install

python -m venv .venv #ここではなにもおきない
source ./.venv/bin/activate
pip install -r requirements.txt

serverless offline start
```

- プラグインインストールはトータル 2 時間くらいかかるかも
- pip install が動かない場合は python3 など適宜使えるものに変更する

## serverless offline start

- serverless offline start で実際に Lambda から DynamoDB へのアクセスが可能となる

```sh
curl "http://localhost:3000/dev/hello"
[{"UserEmail": {"S": "xxx@example.com"}, "UserID": {"S": "1"}, "UserName": {"S": "John"}}, {"UserEmail": {"S": ""}, "UserID": {"S": "2"}, "UserName": {"S": "Doe"}
```

## jq プラグインインストールすると json 見やすくなるのでおすすめ

```sh
sudo apt install jq
```

- 使い方

```sh
curl -s "http://localhost:3000/dev/hello" | jq .
```

## 使い方
- handlerの中のLambdaを修正して使う。もしくは新しいLambdaを追加する。
- 新しいLambdaを追加する場合はserverless.yamlに追記しエンドポイントを作成する

</details>

<details><subject>エラー集</subject>

## ポート被ってる問題

- 普段 8000 使ってると起動時にバッティングするのでチェックして Kill

```sh
lsof -i :8000
kill -9 <PID>
```

</details>
