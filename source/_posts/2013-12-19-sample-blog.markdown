---
layout: post
title: "git rebase --onto で派生元ブランチの変更"
date: 2013-12-19 16:53:13 +0900
comments: true
sharing: true
categories: 
---

本来 master ブランチから派生させるべきだった topic ブランチを、間違って develop ブランチから派生させてしまった場合、rebase --onto を使って、派生元を master ブランチに変更できる。

```
$ git rebase --onto master develop topic
```
