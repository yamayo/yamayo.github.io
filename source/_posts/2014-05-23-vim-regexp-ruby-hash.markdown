---
layout: post
title: "Vim の正規表現で Ruby の Hash のキーと値の間にスペースを追加する"
date: 2014-05-23 21:28:29 +0900
comments: true
sharing: true
published: true
categories: Vim
---

会社で spec ファイルのリファクタリングをしてるんだけど、以下のようなコードが多くて困っていたのです。

``` ruby
class Api::UserController
  ...
  h = { foo:'aaa', bar:'bbb' }
  ...
end
```
キーと値の間にスペースがないんですよ。<br/>
細かいけど、こうゆうの気になっちゃうんです...

正規表現が苦手なボクが置換してみた。（Vim です）
``` ruby
%s/\([A-Za-z0-9]\):\([A-Za-z"'0-9]\)/\1: \2/gc
```
もっと簡単にできそうだなぁ。。
