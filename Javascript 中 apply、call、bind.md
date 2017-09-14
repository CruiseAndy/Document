# Javascript 中 apply、call、bind

在 javascript 中，call 和 apply 都是為了改變某個函數運行時的上下文（context）而存在的，換句話說，就是為了改變函數體內部 this 的指向。
JavaScript 的一大特點是，函數存在「定義時上下文」和「運行時上下文」以及「上下文是可以改變的」這樣的概念。

### example
```javascript=
function fruits() {}
 
fruits.prototype = {
    color: "red",
    say: function() {
        console.log("My color is " + this.color);
    }
}
 
var apple = new fruits;
apple.say();    //My color is red
```

但是如果我們有一個對象banana= {color : “yellow”} ,我們不想對它重新定義 say 方法，那麼我們可以通過 call 或 apply 用 apple 的 say 方法：

```javascript=
banana = {
    color: "yellow"
}
apple.say.call(banana);     //My color is yellow
apple.say.apply(banana);    //My color is yellow
```

所以，可以看出call 和apply 是為了動態改變this 而出現的，當一個object 沒有某個方法（本例子中banana沒有say方法），但是其他的有（本栗子中apple有say方法），我們可以借助call或apply用其它對象的方法來操作。

---
### apply、call 的區別

對於 apply、call 二者而言，作用完全一樣，只是接受參數的方式不太一樣。例如，有一個函數定義如下：

```javascript=
var func = function(arg1, arg2) {
 
};
```

就可以通過如下方式來調用：
```javascript=
func.call(this, arg1, arg2);
func.apply(this, [arg1, arg2])
```

其中 this 是你想指定的上下文，他可以是任何一個 JavaScript 對象(JavaScript 中一切皆對象)，call 需要把參數按順序傳遞進去，而 apply 則是把參數放在數組裡。

JavaScript 中，某個函數的參數數量是不固定的，因此要說適用條件的話，當你的參數是明確知道數量時用 call 。
而不確定的時候用 apply，然後把參數 push 進數組傳遞進去。當參數數量不確定時，函數內部也可以通過 arguments 這個數組來遍歷所有的參數。
為了鞏固加深記憶，下面列舉一些常用用法：
1、數組之間追加

```javascript=
var array1 = [12 , "foo" , {name "Joe"} , -2458]; 
var array2 = ["Doe" , 555 , 100]; 
Array.prototype.push.apply(array1, array2); 
/* array1 值为  [12 , "foo" , {name "Joe"} , -2458 , "Doe" , 555 , 100] */
```

2、獲取數組中的最大值和最小值
```javascript=
var  numbers = [5, 458 , 120 , -215 ]; 
var maxInNumbers = Math.max.apply(Math, numbers),   //458
    maxInNumbers = Math.max.call(Math,5, 458 , 120 , -215); //458
```

number 本身沒有 max 方法，但是 Math 有，我們就可以藉助 call 或者 apply 使用其方法。

3、驗證是否是數組（前提是toString()方法沒有被重寫過）

```javascript=
functionisArray(obj){ 
    returnObject.prototype.toString.call(obj) === '[object Array]' ;
}
```

4、類（偽）數組使用數組方法
```javascript=
var domNodes = Array.prototype.slice.call(document.getElementsByTagName("*"));
```

Javascript中存在一種名為偽數組的對象結構。比較特別的是 arguments 對象，還有像調用 getElementsByTagName , document.childNodes 之類的，它們返回NodeList對像都屬於偽數組。不能應用 Array下的 push , pop 等方法。
但是我們能通過 Array.prototype.slice.call 轉換為真正的數組的帶有 length 屬性的對象，這樣 domNodes 就可以應用 Array 下的所有方法了。
深入理解運用apply、call

下面就借用一道面試題，來更深入的去理解下 apply 和 call 。

定義一個 log 方法，讓它可以代理 console.log 方法，常見的解決方法是：

```javascript=
function log(msg)　{
  console.log(msg);
}
log(1);    //1
log(1,2);    //1
```

接下來的要求是給每一個 log 消息添加一個”(app)”的前輟，比如：

```javascript=
log("hello world");    //(app)hello world
```

該怎麼做比較優雅呢?這個時候需要想到arguments參數是個偽數組，通過 Array.prototype.slice.call 轉化為標準數組，再使用數組方法unshift，像這樣：

```javascript=
function log(){
  var args = Array.prototype.slice.call(arguments);
  args.unshift('(app)');

  console.log.apply(console, args);
};
```

---
### bind 的區別

說完了 apply 和 call ，再來說說bind。 bind() 方法與 apply 和 call 很相似，也是可以改變函數體內 this 的指向。

MDN的解釋是：bind()方法會創建一個新函數，稱為綁定函數，當調用這個綁定函數時，綁定函數會以創建它時傳入bind()方法的第一個參數作為this ，傳入bind() 方法的第二個以及以後的參數加上綁定函數運行時本身的參數按照順序作為原函數的參數來調用原函數。

直接來看看具體如何使用，在常見的單體模式中，通常我們會使用 _this , that , self 等保存 this ，這樣我們可以在改變了上下文之後繼續引用到它。像這樣：

```javascript=
var foo = {
    bar : 1,
    eventBind: function(){
        var _this = this;
        $('.someClass').on('click',function(event) {
            /* Act on the event */
            console.log(_this.bar);     //1
        });
    }
}
```

由於Javascript 特有的機制，上下文環境在eventBind:function(){ } 過渡到$('.someClass').on('click',function(event) { }) 發生了改變，上述使用變量保存this 這些方式都是有用的，也沒有什麼問題。當然使用 bind() 可以更加優雅的解決這個問題：

```javascript=
var foo = {
    bar : 1,
    eventBind: function(){
        $('.someClass').on('click',function(event) {
            /* Act on the event */
            console.log(this.bar);      //1
        }.bind(this));
    }
}
```

在上述代碼裡，bind() 創建了一個函數，當這個click事件綁定在被調用的時候，它的this 關鍵詞會被設置成被傳入的值（這裡指調用bind()時傳入的參數）。因此，這裡我們傳入想要的上下文 this(其實就是 foo )，到 bind() 函數中。然後，當回調函數被執行的時候， this 便指向 foo 對象。再來一個簡單的例子：

```javascript=
var bar = function(){
    console.log(this.x);
}

bar(); // undefined
var func = bar.bind(foo);
func(); // 3
```

這裡我們創建了一個新的函數func，當使用bind() 創建一個綁定函數之後，它被執行的時候，它的this 會被設置成foo ， 而不是像我們調用bar() 時的全局作用域。

有個有趣的問題，如果連續 bind() 兩次，亦或者是連續 bind() 三次那麼輸出的值是什麼呢？像這樣：

```javascript=
var bar = function(){
    console.log(this.x);
}
var foo = {
    x:3
}
var sed = {
    x:4
}
var func = bar.bind(foo).bind(sed);
func(); //?

var fiv = {
    x:5
}
var func = bar.bind(foo).bind(sed).bind(fiv);
func(); //?
```

答案是，兩次都仍將輸出 3 ，而非期待中的 4 和 5 。原因是，在Javascript中，多次 bind() 是無效的。更深層次的原因， bind() 的實現，相當於使用函數在內部包了一個call / apply ，第二次bind() 相當於再包住第一次bind() ,故第二次以後的bind 是無法生效的。

apply、call、bind比較

那麼 apply、call、bind 三者相比較，之間又有什麼異同呢？何時使用 apply、call，何時使用 bind 呢。簡單的一個例子：

```javascript=
var obj = {
    x: 81,
};

var foo = {
    getX: function() {
        return this.x;
    }
}

console.log(foo.getX.bind(obj)());  //81
console.log(foo.getX.call(obj));    //81
console.log(foo.getX.apply(obj));   //81
```

三個輸出的都是81，但是注意看使用 bind() 方法的，他後面多了對括號。

也就是說，區別是，當你希望改變上下文環境之後並非立即執行，而是回調執行的時候，使用 bind() 方法。而 apply/call 則會立即執行函數。

再總結一下：

 * apply 、 call 、bind 三者都是用來改變函數的this對象的指向的；
 * apply 、 call 、bind 三者第一個參數都是this要指向的對象，也就是想指定的上下文；
 * apply 、 call 、bind 三者都可以利用後續參數傳參；
bind 是返回對應函數，便於稍後調用；apply 、call 則是立即調用 。

---

### [Reference](http://web.jobbole.com/83642/)
