---
layout: post
title: "Rails - STI（単一テーブル継承）で Controller をどう書くか"
date: 2014-05-07 22:10:08 +0900
comments: true
sharing: true
published: false
categories: Rails
---

Rails の STI（単一テーブル継承）で Controller をどう書くかというお話です。<br/>
STI の例として、支出と収入の費目のモデルについて考えてみましょう。


``` ruby Single table inheritance
class Category < ActiveRecord::Base; end

class Expense < Category; end

class Incoming < Category: end
```

この場合、



``` ruby routes.rb
resources :expenses, controller: :categories, type: 'Expense'
resources :incomings, controller: :categories, type: 'Incoming'
```
