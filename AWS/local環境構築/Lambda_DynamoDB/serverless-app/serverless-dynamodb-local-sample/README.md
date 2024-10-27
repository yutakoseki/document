# 概要
- serverless frameworkを利用して、DynamoDB Localを利用するサンプル

# 構成
![serverlessframework-dynamodb-local-sample.drawio](./docs/serverlessframework-dynamodb-local-sample.drawio.svg)

# ディレクトリ構成
```
.
├── handler
│   └── hello.py      # lambda handler
├── seeds
│   └── seed.json  # DynamoDB Local 用データセット
├── package-lock.json
├── package.json
├── requirements.txt　 # lambdaで使用するパッケージ
├── .env
└── serverless.yml
```

# 利用方法
```bash
serverless plugin install -n serverless-dynamodb
serverless plugin install -n serverless-python-requirements
serverless plugin install -n serverless-offline

serverless dynamodb install

python -m venv .venv
source ./.venv/bin/activate
pip install -r requirements.txt

serverless offline start
```