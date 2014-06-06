---
layout: post
title: "Rake で before task をフックする"
date: 2014-06-06 22:50:27 +0900
comments: true
sharing: true
published: true
categories: rake
---

Rake タスクの前後に実行するタスクを登録する方法を調べていました。<br/>
https://github.com/guillermo/rake-hooks というのもあるようですが、Build Status が...<br/>
結果的には、以下のようにとてもシンプルな形で実現出来ました。

``` ruby
task main: :environment do
  p :foo
end
task before: :environment do
  p :before
end
task main: :before
```

そもそも何故やりたかったのかと言うと、sidekiq を system を使って実行しようとした時に、<br/>
RAILS_ENV を before タスクで共通的に設定するようにしたかったのでした。

``` ruby
namespace :sidekiq do
  desc "Start sidekiq"
  task start: :environment do
    system("bundle exec sidekiq -C #{Rails.root.join('config', 'sidekiq.yml')}")
  end

  task :sidekiqctl, [:name, :deadline_timeout] => :environment do |t, args|
    system("bundle exec sidekiqctl #{args[:name]} #{Rails.root.join('tmp', 'pids', 'sidekiq.pid')} #{args[:deadline_timeout]}")
  end

  desc "Quiet sends USR1 to sidekiq"
  task quiet: :environment do
    Rake::Task['sidekiq:sidekiqctl'].invoke('quiet')
  end

  desc "Stop sends TERM to sidekiq"
  task stop: :environment do
    Rake::Task['sidekiq:sidekiqctl'].invoke('stop', '10')
  end

  task before: :environment do
    RAILS_ENV = (ENV['RAILS_ENV'] || 'development').dup unless defined?(RAILS_ENV)
  end
  task start: :before
  task sidekiqctl: :before
end
```

上記のようにすることで、通常の rake タスクと同様に RAILS_ENV を設定することができました。
``` sh
$ RAILS_ENV=production bundle exec rake sidekiq:start
```
