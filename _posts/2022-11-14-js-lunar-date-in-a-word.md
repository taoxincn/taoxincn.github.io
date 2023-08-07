---
title:  一行JavaScript代码搞定农历日期
date:   2022-11-14 22:39:00 +0800
categories: [开发, 前端]
tags: [JavaScript]
---

前端项目中有时候会用到农历日期，但农历日期的获取却较为麻烦，下面我们用一行JS代码搞定农历代码，希望对大家有帮助。

```js
new Date().toLocaleString('zh-Hans-u-ca-chinese').replace(/\d+年(.+月)(\d+) \d+:\d+:\d+/, (_, x, y) => x + ['初', '十', '二十', '三十'].at(parseInt(parseInt(y) == 10 ? (parseInt(y) - 1) / 10 : parseInt(y) / 10)) + (parseInt(y) > 10 && parseInt(y) % 10 == 0 ? '' : "十一二三四五六七八九".charAt(parseInt(y) % 10)));
```