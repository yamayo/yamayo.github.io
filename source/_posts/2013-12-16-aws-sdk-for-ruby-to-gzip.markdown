---
layout: post
title: "AWS SDK for Ruby を使ってコンテンツを S3 に gzip 圧縮して保存する"
date: 2013-12-16 03:01:55 +0900
comments: true
sharing: true
categories: 
---

S3 で gzip コンテンツに対応させる方法は、オブジェクトの Metadata に対して、Content-Encoding を指定できるのでこれを使います。
![s3-metadata](/images/posts/2014-12-16/s3-metadata.png)

AWS SDK for Ruby を使って、gzip コンテンツを upload/download するサンプルを Gist に上げました。<br>
<a href="https://gist.github.com/yamayo/7981897" target="_blank">AWS S3 upload/download with gzip</a>
{% gist 7981897 %}

ちなみに、Google Developers によるとオーバーヘッドが発生するため、圧縮するファイルサイズの最小範囲は 150 〜 1000 バイトが推奨されています。<br>

<a href="https://developers.google.com/speed/docs/best-practices/payload?hl=ja#GzipCompression" target="_blank">https://developers.google.com/speed/docs/best-practices/payload?hl=ja#GzipCompression</a>
