---
title: "「Build Your Own Container Using Less than 100 Lines of Go」をやってみた"
date: 2024-05-02T12:48:54+09:00
draft: true
---

- 1 日開発合宿をやった
- ハッカソン形式でサービスを作るのも楽しいが、どちらかというと普段できてない学びを深めたい
- Build your own X を何かやるに決定

### Build your own X

- 身近な技術の実装を 1 からしてみる系
- https://github.com/codecrafters-io/build-your-own-x にまとまっている

## 今回取り組んだもの

https://www.infoq.com/articles/build-a-container-golang/
https://github.com/dora1998/tiny-container

## ハマったこと

### Docker コンテナ上で動かない

### ファイルシステムを隔離するとコマンドが存在しない

https://amasuda.xyz/post/2020-03-07-create-container-with-golang/

## 追加で取り組みたいこと

### cgroup でリソース制限を実装

https://gihyo.jp/admin/serial/01/linux_containers/0003
https://github.com/containerd/cgroups

### mount 周りの制約・挙動を深ぼる

https://qiita.com/hayama17/items/137353f378c74f4ee323

## おわりに

- 三浦海岸とってもよかった
