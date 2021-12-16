---
title: "卒論を支えた技術 #kuac2021"
date: 2021-12-17T02:35:52+09:00
archives: "2021"
tags: ["卒論", "git", "Docker", "TeX"]
author: Dora
---

この記事は [Kyoto University Advent Calendar 2021](https://adventar.org/calendars/6580) 17 日目の記事です．

こんにちは、どら([@d0ra1998](https://twitter.com/d0ra1998))です。
早いもので、3 月に大学を卒業して 9 ヶ月ほどが経とうとしています。今回のアドベントカレンダーは、卒業論文を書いていた頃に少しでもストレスを減らすために工夫していたことを振り返りながらまとめてみようと思います。

## git を使う

御多分に洩れず、原稿は git でバージョン管理を行いました。
ここで活躍したのが、単なるフォルダでも git のリモートリポジトリとして登録できるという機能です。

情報系の研究室であれば、オンプレで GitLab などを運用しているという話もたまに聞きますが、未だに Samba などで共有フォルダがぽんとあるだけという研究室も多いことでしょう。
そのような場合、研究室に行った時に同期する作業が手間となりますが、git のリモートリポジトリとして登録しておくことで

```
$ git push lab main
```

とコマンドを打てば同期が完了するので大変便利です。

この方法は [Pro Git](https://git-scm.com/book/ja/v2/Git%E3%82%B5%E3%83%BC%E3%83%90%E3%83%BC-%E3%83%97%E3%83%AD%E3%83%88%E3%82%B3%E3%83%AB) 内でも `Local プロトコル` として解説されており確認することができます。
たとえば、共有サーバーの自分のフォルダが `/Volumes/dora-lab/home/dora/` 配下である場合、リポジトリのルートで次のコマンドを打てば登録することができます。

```
$ git clone --bare . /Volumes/dora-lab/home/dora/graduation_thesis.git
$ git remote lab /Volumes/dora-lab/home/dora/graduation_thesis.git
```

研究室によってはクラウドストレージの利用などが厳しいところもあると思うので、GitHub が使いづらい場合などにもこの手法は役に立つのではないかと思います。
GitHub を併用する場合でもリモートリポジトリは複数登録できるため、普段は GitHub に push しつつ時々研究室サーバーに push しておくという運用も可能です。

## TeX ファイルをなるべく分割する

たかが卒論といえども、これまでの実験レポートなどと比べると量や修正も桁違いに多くなってきます。
多くの場合は、章分けを行うと思うのでそれに基づいて分けると良いでしょう。

参考までに、自分の場合は以下のようにフォルダを切っていました。
当初はテンプレートの cls ファイルなどを `lib` フォルダに移したり、出力結果を `dist` フォルダに入れたりしたかったのですが、TeX の仕様上ひとひねり必要そうで時間がなかったので断念しています。

```
.
├── assets          # 図のaiファイルや測定写真原本など
│   ├── figure1.pptx
│   └── figure2.ai
├── lib
│   ├── doc         # /usr/local/share/texmf/doc/ に配置するライブラリ群
│   │   └── platex
│   │       └── plautopatch
│   └── tex         # /usr/local/share/texmf/tex/ に配置するライブラリ群
│       └── platex
│           └── plautopatch
├── memo            # 先輩や教員と話したことを雑に残すフォルダ
│   └── 20210120-meeting.md
├── src             # 卒論本体
│   ├── abstract    # アブスト
│   ├── chapters    # 章ごとの原稿
│   │   ├── intro.tex
│   │   ├── ...
│   │   ├── conclusion.tex
│   │   └── acknowledgements.tex
│   └── figures     # TeXファイルから参照する画像
│   │   ├── figure1.png
│   │   └── figure2.pdf
└── tools           # シェルスクリプトとか
```

## ローカルに TeX Live を入れない

これ本当におすすめです。Docker に慣れ親しんでいるなら、ローカルに TeX Live をインストールするのはもうやめましょう。
日本語フォントなども同梱したイメージが有志によって多数公開されています。

- [korosuke613/docker-ubuntu-texlive-ja](https://github.com/korosuke613/docker-ubuntu-texlive-ja)
- [Paperist/texlive-ja](https://github.com/Paperist/texlive-ja)

これに VSCode Remote Containers を組み合わせることで、コンテナ内でも快適に執筆作業が行えます。
なお、ビルドの際は `.latexmkrc` をちゃんと書いて、 `latexmk` コマンドを使うのがおすすめです。
TeX は参照などを更新する際に複数回ビルドが必要ですが、必要なコマンドを必要な分だけ 1 コマンドで実行してくれて大変便利です。

```
$ latexmk main
```

## 終わりに

この記事では、卒論を書くにあたってあまり時間をかけずに行える技術的工夫をいくつか紹介しました。

今回のように一人で書くケースでは、CI を整えかけたものの結局最後はローカルでビルドして即確認するハメになるので諦めましたが、共著者がいる場合などは CI/CD を整えることでより便利にすることもできます([参考](https://blog.ymyzk.com/2017/12/ci-for-writing-papers/))。

どんなに執筆環境を整えたとて、最後は愚直に執筆に向き合うしかないですが、執筆中のストレスを少しでも減らすためにこの記事が役に立てば幸いです。

## 併せて読みたい

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://msakashita.com/entry/management-research-life" data-iframely-url="//cdn.iframe.ly/ciSumAG?card=small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

CAMPHOR-の先輩サカシタさんの記事です。地味にこういう心持ちみたいな話はなかなか学びづらいので、是非一度読んでみてください。

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://note.com/dora1998/n/n73de3bda6a92" data-iframely-url="//cdn.iframe.ly/1qyJRRU?card=small"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>

忙しくて心が疲れたら、京都の四季の写真をまとめた去年の [#kuac2020](https://adventar.org/calendars/5040) の記事もぜひ。
