---
layout: post
title: "RSpec の shared_examples にブロック引数を渡す"
date: 2014-06-20 23:16:05 +0900
comments: true
sharing: true
published: true
categories: RSpec
---

RSpec の shared_examples は便利ですが、複数の example でちょっとだけ内容を変更したい場合があります。
そんなときは shard_examples にブロック引数が渡せるので、それを使って変更できます。

```rb
shared_examples 'Series of processes' do |type|
  it { expect(collection).to receive(:send_messages) }
  it { expect(collection).to receive(:receive_message) }
  if type == 'foo'
    it { expect(User).to receive(:login) }
  else
    it { expect(User).to_not receive(:login) }
  end
end

context 'foo' do
  it_behaves_like 'Series of processes', 'foo'
end

context 'bar' do
  it_behaves_like 'Series of processes', 'bar'
end
```
