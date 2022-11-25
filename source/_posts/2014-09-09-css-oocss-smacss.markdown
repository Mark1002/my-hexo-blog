---
layout: post
title: "CSS 架構方法"
date: 2014-09-09 12:50:45 +0800
comments: true
categories: css
---

以往在寫 CSS 的時候常常會因為隨著網站規模越大，而越顯得不知如何規劃好 CSS，導致越寫越亂，因此 CSS 近幾年來開始導入了物件導向的思維，藉此提高程式碼的重用性。著名的 CSS 框架 bootstrap 也以此為依歸去實作，昨天強者我同事點醒了我這一點，讓我對 CSS的學習又多了新的見解。現今 CSS 架構方法主要有分為兩種，一種叫 OOCSS，Object Oriented CSS 即物件導向 CSS，另一種則叫做 SMACSS, Scalable and Modular Architecture for CSS，讓 CSS 模組化並具備可擴充性。

首先來講 OOCSS, 由 Nicole Sullivan 於2009年所提出，它的核心目標就是增加程式碼的可重用性來降低重複，
並盡可能的對 layout 中的元件做抽象化。

OOCSS兩大原則

**1.結構與外觀分離 Separate structure and skin**   
簡單來講就是將元件樣式中有重複的視覺樣式(像是background 或 border的樣式)提取出來，成為一個共同的 skin 樣式來增加程式碼的重用性。比如以下程式碼

<!--more-->

	#box {
  	    width: 200px;
  		height: 200px;
  		box-shadow: rgba(0, 0, 0, .5) 2px 2px 5px;
  		background: linear-gradient(#ccc, #222);
	}

	#item {
  		width: 100px;
  		height: 30px;
  		box-shadow: rgba(0, 0, 0, .5) 2px 2px 5px;
  		background: linear-gradient(#ccc, #222);
	}

從中可以發現有重複的樣式，因此可以把它們提出來變成共同的 skin。
OOCSS 提倡的理念是可重用性，因此皆建議使用 class 選擇器，不使用 id 或 tag。

	.box {
  	 	width: 200px;
  		height: 200px;
	}

	.item {
  		width: 100px;
  		height: 30px;
	}

	.skin {
		box-shadow: rgba(0, 0, 0, .5) 2px 2px 5px;
  		background: linear-gradient(#ccc, #222);
	}
另一點，此概念也可指減少 CSS 對 HTML 結構的依賴。像不使用 tag 選擇器定義樣式，用class來定義不同的元件。假設以下程式碼中想在 media 裏頭把 img 標籤換成 svg，這時如果 img 本身有套用樣式的話，那 svg 必須在改一次樣式

    <div class="media">
        <img/>
    </div>

但如果將 CSS 與 HTML 結構分開，如此的話直接把 img 改成 svg 就好了，也不會再接觸到 CSS

    <div class="media">
      <div class="image">
        <img src="...."/>
      <div>
    </div>

**2.容器與內容分離 container and content**  
內容元素樣式不會因為相對於外層容器元素的位置改變而受到影響。像這典型的例子中，超連結的樣式如果以`nav ul li a {}` 來表示的話，那只要超連結的位置有所改變，那樣式等於又要重改了，所以 OOCSS 不建議使用 descendant selector，建議使用 class selector來解決

    <div class="nav">
      <ul>
        <li><a>.....</a></li>
        <li><a>.....</a></li>
        <li><a>.....</a></li>
      </ul>
    </div>

SMACSS

{% img right https://smacss.com/img/book-covers.png 200 250 SMACSS %}
OOCSS 與 SMACSS 相比，OOCSS 比較像是廣義的規範，而 SMACSS 就很明確的定義 CSS 的架構，甚至還有出書! https://smacss.com 首先，SMACSS 把網站的 CSS 分成五大目錄:

**1.Base**  
**2.Layout**  
**3.Module**  
**4.State**  
**5.Theme**  

Base rule 就是指網頁CSS的基底，像超連結的顏色、背景顏色等，定義整個網站中元素的初始樣子，由單一的 tag 選擇器所組成，可以在之後被擴充。通常長的如下:

    html, body, form { margin: 0; padding: 0; }  
    input[type=text] { border: 1px solid #999; }  
    a { color: #039; }  
    a:hover { color: #03C; }  

Layout rule 則把一個頁面分成多個區塊，像是 sidebar section、header section 等，而每一個 Layout 裡可放置單個或多個 module。  
    #header, #article, #footer {
      width: 960px;
      margin: auto;
    }
    #article {
      border: solid #CCC;
      border-width: 1px 0 0;
    }
傳統上 layout 是用 id 選擇器，但有時考量到更高的可重用性，像是規模大的網站，可改用 class 選擇器就比較恰當。
Module rule 指設計中具有可重用性的部分，通常指元件，像 login form、widgets等等，在 html 上是一塊元素的集合，而且一個好的module要定義的非常獨立，不會位置換了不同的layout而受影響。

    <div class="fld">
      <span>Folder Name</span>
    </div>

    /* The Folder Module */
    .fld > span {
      padding-left: 20px;
      background: url(icon.png);
    }
State rule 則是定義 Layout 與 module 會在什麼狀態下呈現什麼樣貌。像是 hidden、expanded、size 大小等等。而且 State 的 class 選擇器也有特別的命名方式， **is-狀態名稱**。以下的 msg module 就套用了 state 樣式:

    <div class="msg is-error">
      There is an error!
    </div>

Theme rules也是定義 layout 與 module 的樣式，但不同的點是它比較不強調會因為什麼狀態下呈現甚麼樣貌，而是決定網站整體的樣式，並可替換重新定義樣式。
像這裡將各個 theme 的樣式集中起來成個別的 theme .css檔，要用到即套用到所選擇的 theme 檔即可。

    /* in theme.css */
    .theme-border {
      border-color: purple;
    }
    .theme-background {
      background: linear-gradient( ... );
    }


參考資料  
http://www.slideshare.net/maxdesign/css-oocss-and-smacss?related=1   
https://github.com/doggy8088/CSS-Guidelines   
http://www.slideshare.net/kurotanshi/css-oocss-smacss-bem   
http://blog.chh.tw/posts/oocss-smacss-and-css-guidelines/#reuse-classes   
https://github.com/stubbornella/oocss/wiki   
https://smacss.com