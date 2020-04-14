---
title: 'JetBrains の IDE で ショートカットキーが反応しなくなったのを直す方法'
date: 2020-04-14T19:30:00+09:00
archives: '2020'
tags: ['JetBrains', 'IntelliJ IDEA']
author: Dora
---

小ネタです。JetBrains 系の IDE で、時たま Cmd+C をはじめとする Cmd キーを使うショートカットキーが反応しなくなることがありました。  
状況を調査すると、どうやら Cmd キーと Ctrl キーがスワップして認識されている模様。

「Preferences」→「Keymap」と進み、「Reset」を押せば直りました。

![Keymapのリセット](./jetbrains-keymap-reset.png)

参考:

<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://intellij-support.jetbrains.com/hc/en-us/community/posts/360000420439-DataGrip-Console-Editor-does-not-respond-to-shortcut-keys-but-other-views-do" data-iframely-url="//cdn.iframe.ly/LpOSbYK"></a></div></div><script async src="//cdn.iframe.ly/embed.js" charset="utf-8"></script>
