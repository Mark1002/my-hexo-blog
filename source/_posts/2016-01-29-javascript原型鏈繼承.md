---
title: javascript原型鏈繼承
date: 2016-01-29 16:35:14
categories: javascript
---
**\_proto_**
javascript 的物件導向繼承機制與其他靜態語言 java、c#、c++ 有很大的不同，javascript 是沒有所謂的 class 的，ECMAScript 6 中新介紹的 "class" 只是一種語法糖，並沒有改變原來的機制，實際上還是 Function Object。
javascript 的繼承機制是採用物件繼承物件的方式，即原型鍊繼承（Prototypal inheritance），當中的關鍵就是`_proto_`這個物件屬性，它就好像一個參照指標一樣，被參考的物件就是此物件的原型，例如`rabbit.__proto__ = animal ` 物件`animal`就是物件`rabbit`的原型，物件`rabbit`繼承了物件`animal`，因此`rabbit`可以使用`animal`的`eat`方法，如圖所示
{% asset_img proto4.png %}
{% codeblock lang:javascript %}
var animal = { eats: true }
var rabbit = { jumps: true }
rabbit.__proto__ = animal  // inherit
alert(rabbit.eats) // true
{% endcodeblock %}

而當物件與物件所繼承的parent原型物件皆有同名稱的 method 或屬性時，則呼叫本身的物件的那一個method 或屬性，如以下程式碼表示

{% codeblock lang:javascript %}
var animal = { eats: true }
var fedUpRabbit = { eats: false}
fedUpRabbit.__proto__ = animal  
alert(fedUpRabbit.eats) // false
{% endcodeblock %}

詳細的運作情形是javascript的原型鍊繼承有個往上查找的特性，當在本身物件內scope找不到某個屬性時，就會循著	`_proto_`指向的原型物件去做查找，像以下另一個例子，`rabbit` 呼叫了 `eat` method，

{% codeblock lang:javascript %}
var animal = {
  eat: function() { 
    alert( "I'm full" )
    this.full = true
  }
}

var rabbit = { 
  jump: function() { /* something */ }
}

rabbit.__proto__ = animal  
rabbit.eat()

{% endcodeblock %}

而`rabbit`本身沒有這個 method，因此在執行的時候 interpreter 就會循著原型鍊往上查找，從原型中找到相應的 method並呼叫
{% asset_img proto5.png%}
值得注意的是這邊的`this`是指`rabbit`物件，`rabbit`物件只是去 call parent method 而已，跟`_proto_`無關，因此執行完最後物件的結果圖會長以下這樣
{% asset_img proto6.png %}


不過使用`__proto__`屬性並不適宜所有瀏覽器，因為它並不是一個共同標準，有些瀏覽器的只能內部讀取而已，並不能隨意跟改`__proto__`值，因此就有其他通用方法的出現。

**prototype**

`prototype`就是一個跨瀏覽器常用的方法，不過他需要跟 constructor function 一起使用，像是以下程式碼

{% codeblock lang:javascript %}
function Rabbit(name) { 
  this.name = name
}
var rabbit = new Rabbit('John')
alert(rabbit.name) // John
{% endcodeblock %}

當呼叫了 new function call ，新的 Rabbit 物件就產生了，**而此時 new 也會設定這個新產生物件的`__proto__`屬性指向這個建構子的 prototype 屬性**，就像以下這樣
{% codeblock lang:javascript %}
var animal = { eats: true }

function Rabbit(name) { 
  this.name = name;
}

Rabbit.prototype = animal ;
var rabbit = new Rabbit('John');
alert( rabbit.eats ); // true, because rabbit.__proto__ == animal

{% endcodeblock %}

`Rabbit.prototype = animal`這段程式碼所要表達的意思就是將所有透過`new Rabbit`產生的物件的`__proto__`屬性全部指向`animal`這個物件並當成原型。

還有一點要注意的是，原型鍊在查找有無物件屬性時，只有該物件沒有該屬性時，才會使用原型的屬性，如果直接在物件上設定屬性的值是不會影響原型物件的屬性的，例如：
{% codeblock lang:javascript %}
function Some() {}
Some.prototype.data = 10;
 
var s = new Some();
console.log(s.data);                 // 10
 
s.data = 20;
console.log(s.data);                 // 20
console.log(Some.prototype.data);    // 10
{% endcodeblock %}

以上這就是 javascript 原型鍊繼承的概念。

參考資料
http://javascript.info/tutorial/inheritance
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes