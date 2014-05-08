---
layout: post
title: "STI（単一テーブル継承）で controller をどう書くか？"
date: 2014-05-07 22:10:08 +0900
comments: true
sharing: true
published: false
categories: Rails
---

Rails の STI（単一テーブル継承）で controller をどう書くかというお話です。<br/>
STI の例として、支出と収入の費目のモデルについて考えてみましょう。

``` ruby Single table inheritance
class Category < ActiveRecord::Base; end

class Expense < Category; end

class Incoming < Category: end
```

Controller については、それぞれ実装するのも DRY じゃないので、一つの controller で実装します。
``` ruby
class CategoriesController < ApplicationController
  before_action :set_category, only: %i(update destroy)

  def index
    @categories = category_class.list(user_id)
  end

  def create
    category = category_class.new(category_params)
    if category.save
      head :created
    else
      render json: category.errors.full_messages, status: :unprocessable_entity
    end
  end

  def update
    if @category.update_attributes(category_params)
      head :no_content
    else
      render json: @category.errors.full_messages, status: :unprocessable_entity
    end
  end

  def destroy
    @category.destroy
    head :no_content
  end

  private

  def type
    @type ||= params[:type]
  end

  def category_class
    type.classify.constantize
  end

  def set_category
    @category = Category.find(params[:id])
  end

  def category_params
    params.require(:category).permit(:name,
                                     :display_order,
                                     :visible,
                                     :image_file_name
                                     ).merge(user_id: user_id)
  end
end
```

routes で以下のように定義すると、、、
``` ruby routes.rb
resources :expenses, controller: :categories, type: 'Expense'
resources :incomings, controller: :categories, type: 'Incoming'
```

あらま〜
``` ruby
$ bundle exec rake routes
new_api_expense GET    /api/expenses/new(.:format)          api/categories#new {:format=>"json", :type=>"Expense"}
     edit_api_expense GET    /api/expenses/:id/edit(.:format)     api/categories#edit {:format=>"json", :type=>"Expense"}
          api_expense GET    /api/expenses/:id(.:format)          api/categories#show {:format=>"json", :type=>"Expense"}
                      PATCH  /api/expenses/:id(.:format)          api/categories#update {:format=>"json", :type=>"Expense"}
                      PUT    /api/expenses/:id(.:format)          api/categories#update {:format=>"json", :type=>"Expense"}
                      DELETE /api/expenses/:id(.:format)          api/categories#destroy {:format=>"json", :type=>"Expense"}
  order_api_incomings POST   /api/incomings/order(.:format)       api/categories#order {:format=>"json", :type=>"Incoming"}
        api_incomings GET    /api/incomings(.:format)             api/categories#index {:format=>"json", :type=>"Incoming"}
                      POST   /api/incomings(.:format)             api/categories#create {:format=>"json", :type=>"Incoming"}
     new_api_incoming GET    /api/incomings/new(.:format)         api/categories#new {:format=>"json", :type=>"Incoming"}
    edit_api_incoming GET    /api/incomings/:id/edit(.:format)    api/categories#edit {:format=>"json", :type=>"Incoming"}
         api_incoming GET    /api/incomings/:id(.:format)         api/categories#show {:format=>"json", :type=>"Incoming"}
                      PATCH  /api/incomings/:id(.:format)         api/categories#update {:format=>"json", :type=>"Incoming"}
                      PUT    /api/incomings/:id(.:format)         api/categories#update {:format=>"json", :type=>"Incoming"}
                      DELETE /api/incomings/:id(.:format)         api/categories#destroy {:format=>"json", :type=>"Incoming"}
```
