---
title: 'Terraform+Swagger+APIGateway+Lambdaで始める爆速API開発'
emoji: '😻'
type: 'tech'
topics: ['Terraform', 'APIGateway', 'lambda', 'Swagger']
published: false
---

# はじめに

## 本記事では以下の API をします

リクエスト

```
$ curl https://<domainname>/users/hoge
```

レスポンス

```
$ {message:your name is hoge}
```

## 本記事は以下の順序で説明します

1. Terraform と Swagger のみで API のインターフェイスを構築します
2. API のインターフェイスが受け取った入力を Lambda に渡します
3. Lambda の出力を API のインターフェイスに返します

# Terraform で APIGateway リソースを定義しよう

- APIGateway を宣言します
- body に Swagger の YAML ファイルを代入します

```js
resource "aws_api_gateway_rest_api" "api" {
  name        = "MyAPIGateway"
  description = "Created by Terraform"
  body        = data.template_file.swagger.rendered
  endpoint_configuration {
    types = ["REGIONAL"]
  }
}
```

- Swagger ファイルを Terraform で読み込みます

:::message
Swagger ファイルは Terraform からは読み取り専用となるので data リソースとして宣言します｡
[参考](https://docs.usacloud.jp/terraform-v1/configuration/resources/data_resource/)
:::

```js
data "template_file" "swagger" {
  // file関数でYAMLを読み込み
  template = file("./swagger.yml")
  // swagger.yml内で呼び出す変数を渡す
  vars = {
    title                   = "MyAPIGateway"
    aws_region_name         = "ap-northeast-1"
    lambda_function_arn = var.testfunction_arn
  }
}
```
