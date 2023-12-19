---
title: rescueとrescue_fromの使い方
tags:
  - Ruby on Rails
  - rescue
  - rescue_from
  - 例外処理
  - エラーハンドリング
private: false
updated_at: ""
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

Ruby on Rails の `rescue` と `rescue_from` は、予期しないエラーに効果的に対処するための機能です。
この記事では、`rescue` と `rescue_from` の基本的な使い方をまとめてみました。

# rescue

rescue は、プログラム内で発生する特定のエラーを効果的に捕捉し、対処するための機能です。
これにより、エラー発生時の適切な対応やプログラムの安定稼働が可能になります。

### 基本構文

rescue を使うことで、特定のエラーが発生した時に特定のコードを実行するよう設定できます。
基本的な形式は以下のようになります。

```ruby
begin

# 例外が発生する可能性のあるコード

rescue [例外クラス]

# 例外が発生した時に実行するコード

end
```

### 使用例

例えば、ユーザーがデータベースに存在しない ID でアクセスしたときにエラーを捕捉する場合、以下のように記述できます。

```ruby
begin
  @user = User.find(params[:id])
rescue ActiveRecord::RecordNotFound
  redirect_to root_path, alert: "ユーザーが見つかりません"
end
```

1. `User.find` メソッドで `ActiveRecord::RecordNotFound` 例外が発生
2. ユーザーをルートパスにリダイレクト
3. `「ユーザーが見つかりません」`と警告メッセージを表示

のような流れになります。

# rescue_from

一方、rescue_from は Rails のコントローラーレベルで例外を捕捉するためのメソッドです。
これにより、複数のアクションにまたがる例外のハンドリングを一箇所で管理することができます。

### 基本構文

rescue_from は、特定の例外に対して共通のレスポンスを定義する場合に便利です。
構文は以下のようになります。

```ruby
class ApplicationController < ActionController::Base
  rescue_from [例外クラス], with: :[例外を処理するメソッド名]
end
```

### 使用例

例えば、すべてのコントローラーで ActiveRecord::RecordNotFound 例外を捕捉して共通の処理を行う場合、以下のように記述できます：

```ruby
class ApplicationController < ActionController::Base
  rescue_from ActiveRecord::RecordNotFound, with: :record_not_found

  private

  def record_not_found
  redirect_to root_path, alert: "指定されたレコードが見つかりません"
  end
end
```

このコードでは、アプリケーション内のどのコントローラーでも下記のような流れで処理が行われます。

1. ActiveRecord::RecordNotFound 例外が発生
2. record_not_found メソッドが呼ばれる
3. ルートパスにリダイレクト
4. `指定されたレコードが見つかりません`という警告メッセージを表示

# 参考

https://railsguides.jp/action_controller_overview.html#rescue
