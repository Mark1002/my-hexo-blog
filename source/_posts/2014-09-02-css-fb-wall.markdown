---
layout: post
title: facebook-wall layout 學習小心得
date: 2014-09-02 18:17:01 +0800
comments: true
categories: css
---
今天都在研究fb塗鴉牆的layout寫法，有興趣的自己當然要試著模仿寫寫看啦~不過過程一波三折，一開始怎麼寫怎麼跑版，有幸得到強者同事指點讓我釐清了不少CSS基本觀念。以下是其中的關鍵
{% iframe http://jsbin.com/xubiw/1/embed?html,css,output %}

程式碼看起來相當簡單，但裡頭卻有一些重要的基本觀念。首先是當textarea不管寬度怎麼樣去延展，都不會被push down到圖片之下。這一步驟有個關鍵屬性`width: 100%`定義了textarea可以隨著上層元素寬度變化改變長寬，而這也正fb塗鴉牆可以鑲嵌到不同網站寬度的特性。
不過當width設100%時, 由於寬度等同於上層元素，若裡面還有其他元素，像是這裡的img，這樣textarea就會超出外層元素範圍，因此我這裡又做了一些操作利用`padding`與`absolute` 的特性定位如此`textarea`再延展的時候就不會被push down了。

最後還有一個屬性也很重要那就是 box-sizing。在Box Model中`padding`、`border`是會加在元素之外的，因此元素實際的寬高是本身的寬高再加上padding、border的寬高，而這個屬性可以決定一個矩形元素的padding及border會被加在元素之外還是被包含在元素之內。簡單來講，就是可以改變box model的預設行為。這裡我設定

	box-sizing: border-box;
讓padding包含在元素之內如此寬度就維持在 100% 而不是預設會跑版的 100% + 40px。

