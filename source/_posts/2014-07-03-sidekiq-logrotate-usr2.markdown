---
layout: post
title: "sidekiq で logrotate を使うときは USR2 シグナルを送る"
date: 2014-07-03 11:45:57 +0900
comments: true
sharing: true
published: true
categories: Sidekiq
---

Unicorn に USR1 のシグナルを送ることでログファイルを reopen 出来ることは知っていたのですが、<br/>
Sidekiq だと下記にあるように USR2 なんですね。<br/>
http://github.com/mperham/sidekiq/wiki/Signals

以下は同プロジェクトに Unicorn と Sidekiq がある場合の、logrotate の書き方の例です。

```
/data/my_rails_app/shared/log/*.log {
  create 0644 yamayo yamayo
  daily
  missingok
  rotate 10
  size 10M
  dateext
  notifempty
  copytruncate

  compress
  delaycompress

  lastaction
    unicorn_pid=/data/my_rails_app/shared/tmp/pids/unicorn.pid
    test -s $pid && kill -USR1 "$(cat $unicorn_pid)"

    sidekiq_pid=/data/my_rails_app/shared/tmp/pids/sidekiq.pid
    test -s $pid && kill -USR2 "$(cat $sidekiq_pid)"
  endscript
}
```

