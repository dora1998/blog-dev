---
title: "iOS Safari で常に正確な 100vh を取得するのは難しい"
slug: real-100vh-difficulty
date: 2022-07-11T15:00:00+09:00
archives: "2022"
tags: ["CSS", "Safari"]
---

LP などのビジュアルを押し出すサイトで、ファーストビューいっぱいに画像などを表示するといった実装がしばしばあります(こんなやつ)。

<p class="codepen" data-height="300" data-default-tab="result" data-slug-hash="KKozxMd" data-user="dora1998" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/dora1998/pen/KKozxMd">
  Untitled</a> by Minoru Takeuchi (<a href="https://codepen.io/dora1998">@dora1998</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

この例のように、画面の高さ分確保するには `100vh` を使うのが一般的ですが、iOS Safari で `100vh` を使うとアドレスバー表示時にはみ出してしまうということは非常によく知られています。

<!-- textlint-disable -->

{{< figure align="center" alt="iOS Safari で 100vh 指定した領域の下部に表示した要素がアドレスバーに被っているキャプチャ" caption="100vh でアドレスバー分は考慮されない" src="./100vh-with-addressbar.png" width="300" >}}

<!-- textlint-enable -->

## よくある JS での解決策

これに対して、JS を使って実際の高さを取得して CSS 変数や `style` プロパティを通して反映する方法がしばしば見られます。
この場合、初期ロードに加えて `resize` イベントのリスナーを設定して更新するのが一般的です。

- [iOS でも 100vh をいい具合に調整して画面の高さいっぱいに要素を表示させる](https://zenn.dev/tak_dcxi/articles/2ac77656aa94c2cd40bf#%E5%BE%93%E6%9D%A5%E3%81%A9%E3%81%8A%E3%82%8Ajavascript%E3%81%A7%E9%AB%98%E3%81%95%E3%82%92%E8%A8%88%E7%AE%97%E3%81%97%E3%81%9F%E3%81%BB%E3%81%86%E3%81%8C%E5%AE%89%E5%AE%9A%E3%81%97%E3%81%A6%E3%81%84%E3%82%8B%E4%BB%B6)
- [The trick to viewport units on mobile | CSS-Tricks - CSS-Tricks](https://css-tricks.com/the-trick-to-viewport-units-on-mobile/)

```css
.gamen-ippai {
  height: 100vh; /* Fallback */
  height: calc(var(--vh, 1vh) * 100);
}
```

```javascript
const setFillHeight = () => {
  let vh = window.innerHeight * 0.01;
  document.documentElement.style.setProperty("--vh", `${vh}px`);
};

window.addEventListener("resize", setFillHeight);
setFillHeight(); // 初期表示時に一度実行する
```

<!-- textlint-disable -->

{{< figure align="center" alt="iOS Safari で innerHeight から取得した 100vh で描画したキャプチャ" caption="innerHeight でアドレスバー除いた領域が取得できる" src="./calc-100vh-from-innerHeight.png" width="300" >}}

<!-- textlint-enable -->

## JS で高さを設定する際の課題

一見、これはうまく動作しているように見えます。しかし、実際に触ってみると、アドレスバーの出現・消失時に追従がどことなくぎこちないです。
ちなみに、これは同じ iOS でもアプリ内ブラウザではスムーズに追従し、異なる挙動を示します。

{{< flex justify="center" gap="16px" >}}

<figure class="align-center">
<video controls width="250">
<source src="./resize-event-inapp.mp4" type="video/mp4">
</video>
<figcaption>アプリ内ブラウザ</figcaption>
</figure>

<figure class="align-center">
<video controls width="250">
<source src="./resize-event-safari.mp4" type="video/mp4">
</video>
<figcaption>Safari</figcaption>
</figure>

{{< /flex >}}

## 原因

iOS Safari で実際に resize イベントが発火する様子を記録したのが以下の動画です。

<figure class="align-center">
<video controls width="250">
<source src="./resize-event-log.mp4" type="video/mp4">
</video>
<figcaption>ブラウザの UI 伸縮時に resize イベントが発火する様子</figcaption>
</figure>

このように、iOS Safari でアドレスバーが出現・消失した際（この動画では 710px と 629px のとき）の `resize` イベントは少し経ってから発火していることがわかりました。
対してアプリ内ブラウザではリアルタイムに発火しており、前の章での検証と一致します。

## 対応策

`resize` イベント自体はすぐに呼ばれないものの、 `innerHeight` の値自体は常に正確な値が返ってきます。
よって、`resize` イベントに加えて何らかの方法で `innerHeight` を取得して更新し続けることで一応ほぼラグなく追従できます。

この問題が発生するのはタッチデバイスなので、例えば以下の Stack Overflow の回答のように対象要素の `touchmove` をイベントトリガーとして使用します。

- [javascript - Mobile browsers don't fire up resize event when hiding address bar - Stack Overflow ](https://stackoverflow.com/a/45982345)

```javascript
const setFillHeight = () => {
  // 変更なし
};

window.addEventListener("resize", setFillHeight);
// ↓追加
document
  .querySelector(".gamen-ippai")
  .addEventListener("touchmove", setFillHeight);
setFillHeight();
```

<figure class="align-center">
<video controls width="250">
<source src="./resize-and-touchmove.mp4" type="video/mp4">
</video>
<figcaption>resize と touchmove を組み合わせた様子</figcaption>
</figure>

これはこれでガクガク見えてしまう気もするので悩ましいですが、一応やりたかったことは達成できました。

## 他の方法での検証結果

`touchmove` 以外にも以下の方法が考えられましたが、以下の理由でそれぞれ不適と判断しました。

### `requestAnimationFrame` でひたすら更新

- 🙆‍♂️ ブラウザの描画タイミングにスタイル更新を合わせることができる
- 🙅‍♂️ 操作の有無に関わらず常に発火され続けてしまう
  - もしくは自分で Intersection Observer などを用いてリスナーのつけ外しが必要

### `position: fixed` で固定

- 🙆‍♂️ JS なしでアドレスバーの有無に関わらずちょうどいっぱいの領域を確保できる
- 🙅‍♂️ `resize` イベント同様、**少し遅れて更新される**
- 🙅‍♂️ ページ内をスクロールしても位置が維持されてしまうため、使い方が限られる

### `100dvh` を使う

- 🙆‍♂️ JS なしでアドレスバーの有無に関わらずちょうどいっぱいの領域を確保できる
- 🙅‍♂️ `resize` イベント同様、**少し遅れて更新される**
- 🙅‍♂️ iOS 15.4 以降とまだサポート範囲が限られる ([参考](https://caniuse.com/viewport-unit-variants))

## 感想

常に厳密に viewport いっぱい表示するという要件は達成できたものの、あまりスマートとは言えない対応策となってしまいました。

しかしながら、パフォーマンス向上や省電力などを考えると、そもそもモバイルブラウザでイベントが throttle されているのはある程度仕方ない面もあるでしょう。
このような制約を予めエンジニア・デザイナーが把握しておくことがより良いアウトプットにつながると感じました。

今回のような細かい点に目を瞑れば、後半にサラッと書きましたが `dvh` はとても楽で便利なので、早く iOS 15.4 以降だけの世界になってほしいものです…。

<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
