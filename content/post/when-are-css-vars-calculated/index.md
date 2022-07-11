---
title: "CSS Variablesの相対的な値はいつ計算されるのか"
date: 2022-04-16T16:30:49+09:00
archives: "2022"
tags: ["CSS"]
draft: true
---

CSS変数とはこんなやつ

```
:root {
  --space: 8px;
}

div {
  margin: 0 calc(var(--space) * 2);
}
```
https://developer.mozilla.org/ja/docs/Web/CSS/Using_CSS_custom_properties

## 事の発端

スクロールバーの幅を取得する方法を調べていた際に、こんなコードがあった
https://stackoverflow.com/a/56382662

```
body {
    --scrollbar-width: calc(100vw - 100%);
}

.container { 
    max-width: calc(100vw - var(--scrollbar-width)); 
}
```

結論から言うとこれは動かない。変数を使用した際の `100%` が適用した要素での `100%` になってしまうから

では、仕様はどうなっているのか調べてみた

## CSS変数が展開されることはあるのか


