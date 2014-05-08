---
layout: post
title: "Rails 4.1 の enum と validation"
date: 2014-05-08 00:23:41 +0900
comments: true
sharing: true
published: true
categories: Rails
---

Rails 4.1 から導入されている Active Record enums の値を validates の inclusion に指定した場合、

``` ruby
class User < ActiveRecord::Base
  enum gender: %i(male female)

  validates :gender,
    presence: true,
    inclusion: { in: proc { genders.keys } }

end
```

以下のように validation が正常に起動して errors に入ることを期待したんですが、
``` sh
$ bundle exec rails c
> user = User.new(gender: 2)
> user.valid?
=> false
> user.errors.messages
=> {:gender=>["is not included in the list"]}
```

インスタンス生成時点で、ArgumentError が発生してしまったのでした。
``` sh
> user = User.new(gender: 2)
ArgumentError: '2' is not a valid gender
```
<br/>
ここにあるように、パフォーマンス上の理由から現在はユーザの入力エラーではなく、アプリケーションエラーと見なされてしまうとのこと。<br/>
<a href="https://github.com/rails/rails/issues/13971" target="_blank">https://github.com/rails/rails/issues/13971</a>
<br/>
出来れば validation とセットで使いたいと思う今日この頃でした。。
