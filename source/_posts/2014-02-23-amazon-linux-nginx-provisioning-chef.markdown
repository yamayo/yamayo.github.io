---
layout: post
title: "Amazon Linux 上に chef と Vagrant を使って nginx の最新バージョンをプロビジョニングする"
date: 2014-02-23 08:30:31 +0900
comments: true
sharing: true
categories: 
---

Amazon Linux 上で yum パッケージから nginx の最新バージョンをインストールするには、 /etc/yum.repos.d/nginx.repo に以下を追加する必要があります。

```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```


これを chef のレシピでやる必要があるのですが、以下のように書きました。
``` ruby
yum_repository 'nginx' do
  description 'Nginx.org Repository'
  baseurl     'http://nginx.org/packages/centos/6/$basearch/'
  gpgcheck    false
  priority    '1'
  action :create
end
```

以下にサンプルをアップしています。<br>
https://github.com/yamayo/chef-repo
