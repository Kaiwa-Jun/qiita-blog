---
title: jbuidlerでJSONを生成する
tags:
  - Ruby on Rails
  - jbuilder
  - JSON
private: false
updated_at: ""
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

jbuilder は、Ruby on Rails で JSON を生成するためのテンプレートエンジンです。この記事では、jbuilder の基本的な使い方をまとめてみました。

<!-- # jbuidler とは何か？

jbuilder は、Ruby on Rails において、JSON 形式のデータを生成するためのテンプレートエンジンです。Rails アプリケーションにおいて、データを JSON 形式で提供する必要がある場合、jbuilder はそのプロセスを簡単かつ効率的にします。以下に、jbuilder の主な特徴をいくつか挙げます： -->

# 環境設定

### Gem のインストール

Gemfile に jbuilder を追加するために、以下の行を追記します。

```ruby:Gemfile
gem 'jbuilder'
```

その後、以下のコマンドを実行して、Gem をインストールします。

```bash
  bundle install
```

# 使い方

### controller ファイルの設定

コントローラーで、JSON 形式でデータを返すように設定します。
例えば、respond_to メソッドを使用して、JSON 形式のレスポンスを許可することができます。

```ruby:app/controllers/UsersController.rb
class UsersController < ApplicationController
  def index
    @users = User.all

    respond_to do |format|
      format.json { render json: @users }
    end
  end
end
```

### 基本的な JSON の生成

app/views ディレクトリ内に、.jbuilder 拡張子のファイルを作成します。
例えば、ユーザーリストを表示するための index.json.jbuilder を作成する場合、以下のような内容になります。

```ruby:app/views/users/index.json.jbuilder
json.array! @users, :id, :name, :email
```

@users に格納されたユーザーの配列を JSON 形式で出力します。各ユーザーの id、name、email が JSON 配列の要素として表示されます。

<!-- ### ネストされた JSON の生成

```ruby:app/views/users/index.json.jbuilder
json.array! @users do |user|
  json.extract! user, :id, :name, :email
  json.profile do
    json.extract! user.profile, :bio, :age
  end
end
``` -->

### array! メソッド

配列の各要素に対してブロックを実行し、その結果を JSON 配列として出力します。

```ruby:app/views/users/index.json.jbuilder
json.array! @users do |user|
  json.id user.id
  json.name user.name
  json.email user.email
end
```

```ruby:出力例
[
  {
    "id": 1,
    "name": "Alice",
    "email": "alice@example.com"
  },
  {
    "id": 2,
    "name": "Bob",
    "email": "bob@example.com"
  }
]
```

この例では、@users 配列の各ユーザーについて、ブロック内で json.id, json.name, json.email を使用し、それぞれのユーザーの id、name、email の情報を JSON 配列に変換しています。

### extract! メソッド

特定のオブジェクトから複数の属性を一括で抽出し、JSON オブジェクトを構築することができます。

```ruby:app/views/users/index.json.jbuilder
json.extract! @user, :id, :name, :email
```

```ruby:出力例
[
  {
    "id": 1,
    "name": "Alice",
    "email": "alice@example.com"
  },
]
```

特定のユーザー（@user）から id、name、email のみを選択的に抽出し、JSON オブジェクトに変換しています。

:::note info
例えば、ユーザーオブジェクトに status、date_of_birth、address などの他の属性が存在しても、指定された id、name、email のみが JSON 出力に含まれます。
:::

### merge! メソッド

与えられたハッシュやオブジェクトの内容を現在の JSON オブジェクトにマージします。

```ruby:app/views/users/index.json.jbuilder
json.array! @users do |user|
  json.merge! status: user.status
end
```

```ruby:出力例
[
  {
    "id": 1,
    "name": "Alice",
    "email": "alice@example.com",
    "status": "active"
  },
  {
    "id": 2,
    "name": "Bob",
    "email": "bob@example.com",
    "status": "inactive"
  }
]
```

この例では、各ユーザーの ID、名前、メールアドレスに加えて、単一の status 属性を追加しています。

:::note info
user.attributes と記述すると、オブジェクトの中身のすべての値を返すことができます。

```ruby:app/views/users/index.json.jbuilder
json.array! @users do |user|
  json.merge! user.attributes
end
```

```ruby:出力例
[
  {
    "id": 1,
    "name": "Alice",
    "email": "alice@example.com",
    "status": "active",
    "date_of_birth": "1990-01-01",
    "address": "123 Main Street"
  },
  {
    "id": 2,
    "name": "Bob",
    "email": "bob@example.com",
    "status": "inactive",
    "date_of_birth": "1992-02-02",
    "address": "456 Elm Street"
  }
]
```

この例では、ユーザーの全属性を JSON オブジェクトにマージしています。

:::

# 参考

https://railsguides.jp/action_view_overview.html
