# JavaScript 模塊化入門Ⅰ：理解模塊

作為一名JS初學者。假如你聽到了一些諸如“模塊化構建&模塊化載入” “Webpack&Browserify” 或者 “AMD&CMD”之類的術語，肯定瞬間就凌亂了。

JavaScript的模塊化聽起來挺深奧，可其實理解它對開發者來說特別實用。

## 什麼是模塊化
稱職的作家會把他的書分章節和段落；好的程序員會把他的代碼分成模塊。

就好像書籍的一章，模塊僅僅是一坨代碼而已。

好的代碼模塊分割的內容一定是很合理的，便於你增加減少或者修改功能，同時又不會影響整個系統。

## 為什麼要使用模塊
模塊化可以使你的代碼低耦合，功能模塊直接不相互影響。我個人認為模塊化主要有以下幾點好處：

1. 可維護性：根據定義，每個模塊都是獨立的。良好設計的模塊會盡量與外部的代碼撇清關係，以便於獨立對其進行改進和維護。維護一個獨立的模塊比起一團凌亂的代碼來說要輕鬆很多。

2. 命名空間：在JavaScript中，最高級別的函數外定義的變量都是全局變量（這意味著所有人都可以訪問到它們）。也正因如此，當一些無關的代碼碰巧使用到同名變量的時候，我們就會遇到“命名空間污染”的問題。

這樣的問題在我們開發過程中是要極力避免的。

後面的內容裡我也會舉一些具體的例子來說明這一點。

3. 可複用性：現實來講，在日常工作中我們經常會復制自己之前寫過的代碼到新項目中。

複製粘貼雖然很快很方便，但難道我們找不到更好的辦法了麼？要是……有一個可以重複利用的模塊豈不妙哉？

## 如何引入模塊
### 模塊模式
模塊模式一般用來模擬類的概念（因為原生JavaScript並不支持類，雖然最新的ES6裡引入了Class不過還不普及）這樣我們就能把公有和私有方法還有變量存儲在一個對像中——這就和我們在Java或Python裡使用類的感覺一樣。這樣我們就能在公開調用API的同時，仍然在一個閉包範圍內封裝私有變量和方法。

實現模塊模式的方法有很多種，下面的例子是通過匿名閉包函數的方法。 （在JavaScript中，函數是創建作用域的唯一方式。）

### 例1：匿名閉包函數

```javascript=
(function () {
  // 在函数的作用域中下面的变量是私有的

  var myGrades = [93, 95, 88, 0, 55, 91];

  var average = function() {
    var total = myGrades.reduce(function(accumulator, item) {
      return accumulator + item}, 0);

      return 'Your average grade is ' + total / myGrades.length + '.';
  }

  var failing = function(){
    var failingGrades = myGrades.filter(function(item) {
      return item < 70;});

    return 'You failed ' + failingGrades.length + ' times.';
  }

  console.log(failing());

}());

// 控制台显示：'You failed 2 times.'
```

通過這種構造，我們的匿名函數有了自己的作用域或“閉包”。這允許我們從父（全局）命名空間隱藏變量。

這種方法的好處在於，你可以在函數內部使用局部變量，而不會意外覆蓋同名全局變量，但仍然能夠訪問到全局變量，如下所示：

```javascript=
var global = 'Hello, I am a global variable :)';

(function () {
  // 在函数的作用域中下面的变量是私有的

  var myGrades = [93, 95, 88, 0, 55, 91];

  var average = function() {
    var total = myGrades.reduce(function(accumulator, item) {
      return accumulator + item}, 0);

    return 'Your average grade is ' + total / myGrades.length + '.';
  }

  var failing = function(){
    var failingGrades = myGrades.filter(function(item) {
      return item < 70;});

    return 'You failed ' + failingGrades.length + ' times.';
  }

  console.log(failing());
  console.log(global);
}());

// 控制台显示：'You failed 2 times.'
// 控制台显示：'Hello, I am a global variable :)'
```

要注意的是，一定要用括號把匿名函數包起來，以關鍵詞function開頭的語句總是會被解釋成函數聲明（JS中不允許沒有命名的函數聲明），而加上括號後，內部的代碼就會被識別為函數表達式。其實這個也叫作立即執行函數（IIFE）感興趣的同學可以[在這裡了解更多][1]

### 例2：全局引入

另一種比較受歡迎的方法是一些諸如jQuery的庫使用的全局引入。和我們剛才舉例的匿名閉包函數很相似，只是傳入全局變量的方法不同：

```javascript=
(function (globalVariable) {

  // 在函数的作用域中下面的变量是私有的
  var privateFunction = function() {
    console.log('Shhhh, this is private!');
  }

  // 通过全局变量设置下列方法的外部访问接口
  // 与此同时这些方法又都在函数内部

  globalVariable.each = function(collection, iterator) {
    if (Array.isArray(collection)) {
      for (var i = 0; i < collection.length; i++) {
        iterator(collection[i], i, collection);
      }
    } else {
      for (var key in collection) {
        iterator(collection[key], key, collection);
      }
    }
  };

  globalVariable.filter = function(collection, test) {
    var filtered = [];
    globalVariable.each(collection, function(item) {
      if (test(item)) {
        filtered.push(item);
      }
    });
    return filtered;
  };

  globalVariable.map = function(collection, iterator) {
    var mapped = [];
    globalUtils.each(collection, function(value, key, collection) {
      mapped.push(iterator(value));
    });
    return mapped;
  };

  globalVariable.reduce = function(collection, iterator, accumulator) {
    var startingValueMissing = accumulator === undefined;

    globalVariable.each(collection, function(item) {
      if(startingValueMissing) {
        accumulator = item;
        startingValueMissing = false;
      } else {
        accumulator = iterator(accumulator, item);
      }
    });

    return accumulator;

  };

 }(globalVariable));
```

在這個例子中，globalVariable 是唯一的全局變量。這種方法的好處是可以預先聲明好全局變量，讓你的代碼更加清晰可讀。

### 例3：對象接口

像下面這樣，還有一種創建模塊的方法是使用獨立的對象接口：

```javascript=
var myGradesCalculate = (function () {

  // 在函数的作用域中下面的变量是私有的
  var myGrades = [93, 95, 88, 0, 55, 91];

  // 通过接口在外部访问下列方法
  // 与此同时这些方法又都在函数内部

  return {
    average: function() {
      var total = myGrades.reduce(function(accumulator, item) {
        return accumulator + item;
        }, 0);

      return'Your average grade is ' + total / myGrades.length + '.';
    },

    failing: function() {
      var failingGrades = myGrades.filter(function(item) {
          return item < 70;
        });

      return 'You failed ' + failingGrades.length + ' times.';
    }
  }
})();

myGradesCalculate.failing(); // 'You failed 2 times.' 
myGradesCalculate.average(); // 'Your average grade is 70.33333333333333.'
```

### 例4：揭示模塊模式 Revealing module pattern

這和我們之前的實現方法非常相近，除了它會確保，在所有的變量和方法暴露之前都會保持私有：

```javascript=
var myGradesCalculate = (function () {

   // 在函数的作用域中下面的变量是私有的
  var myGrades = [93, 95, 88, 0, 55, 91];

  var average = function() {
    var total = myGrades.reduce(function(accumulator, item) {
      return accumulator + item;
      }, 0);

    return'Your average grade is ' + total / myGrades.length + '.';
  };

  var failing = function() {
    var failingGrades = myGrades.filter(function(item) {
        return item < 70;
      });

    return 'You failed ' + failingGrades.length + ' times.';
  };

  // 将公有指针指向私有方法

  return {
    average: average,
    failing: failing
  }
})();

myGradesCalculate.failing(); // 'You failed 2 times.' 
myGradesCalculate.average(); // 'Your average grade is 70.33333333333333.'
```

到這裡，其實我們只聊了模塊模式的冰山一角。感興趣的朋友可以閱讀更詳細的資料：

 - [深入理解JavaScript 模塊模式][2]
 - [深入理解JavaScript系列（3）：全面解析Module模式][3]

## CommonJS & AMD

上述的所有解決方案都有一個共同點：使用單個全局變量來把所有的代碼包含在一個函數內，由此來創建私有的命名空間和閉包作用域。

雖然每種方法都比較有效，但也都有各自的短板。

有一點，作為開發者，你必須清楚地了解引入依賴文件的正確順序。就拿Backbone.js來舉個例子，想要使用Backbone就必須在你的頁面裡引入Backbone的源文件。

然而Backbone又依賴 Underscore.js，所以Backbone的引入必須在其之後。

而在工作中，這些依賴管理經常會成為讓人頭疼的問題。

另外一點，這些方法也有可能引起命名空間衝突。舉個例子，要是你碰巧寫了倆重名的模塊怎麼辦？或者你同時需要一個模塊的兩個版本時該怎麼辦？

難道就沒有不通過全局作用域來實現的模塊方法麼？

當然是有的。

接下來介紹兩種廣受歡迎的解決方案：CommonJS 和 AMD.

### CommonJS

CommonJS 擴展了JavaScript聲明模塊的API.

CommonJS模塊可以很方便得將某個對象導出，讓他們能夠被其他模塊通過 require 語句來引入。要是你寫過 Node.js 應該很熟悉這些語法。

通過CommonJS，每個JS文件獨立地存儲它模塊的內容（就像一個被括起來的閉包一樣）。在這種作用域中，我們通過 module.exports 語句來導出對象為模塊，再通過 require 語句來引入。

還是舉個直觀的例子吧：

```javascript=
function myModule() {
  this.hello = function() {
    return 'hello!';
  }

  this.goodbye = function() {
    return 'goodbye!';
  }
}

module.exports = myModule;
```

通過指定導出的對象名稱，CommonJS模塊系統可以識別在其他文件引入這個模塊時應該如何解釋。

然後在某個人想要調用 myMoudle 的時候，只需要 require 一下：

```javascript=
var myModule = require('myModule');

var myModuleInstance = new myModule();
myModuleInstance.hello(); // 'hello!'
myModuleInstance.goodbye(); // 'goodbye!'
```

這種實現比起模塊模式有兩點好處：

1. 避免全局命名空間污染
2. 明確代碼之間的依賴關係

並且這種書寫方式也非常舒服友好，我自己很喜歡。

需要注意的一點是，CommonJS以服務器優先的方式來同步載入模塊，假使我們引入三個模塊的話，他們會一個個地被載入。

它在服務器端用起來很爽，可是在瀏覽器裡就不會那麼高效了。畢竟讀取網絡的文件要比本地耗費更多時間。只要它還在讀取模塊，瀏覽器載入的頁面就會一直卡著不動。 （在下一篇第二部分的教程裡我們會討論如何解決這個問題）

### AMD

CommonJS已經挺不錯了，但假使我們想要實現異步加載模塊該怎麼辦？答案就是Asynchronous Module Definition（異步模塊定義規範），簡稱AMD.

通過AMD載入模塊的代碼一般這麼寫：

```javascript=
define(['myModule', 'myOtherModule'], function(myModule, myOtherModule) {
  console.log(myModule.hello());
});
```

這裡我們使用 define 方法，第一個參數是依賴的模塊，這些模塊都會在後台無阻塞地加載，第二個參數則作為加載完畢的回調函數。

回調函數將會使用載入的模塊作為參數。在這個例子裡就是 myMoudle 和 myOtherModule.最後，這些模塊本身也需要通過 define 關鍵詞來定義。

拿 myModule 來舉個例子：

```javascript=
define([], function() {

  return {
    hello: function() {
      console.log('hello');
    },
    goodbye: function() {
      console.log('goodbye');
    }
  };
});
```

重申一下，不像CommonJS，AMD是優先瀏覽器的一種異步載入模塊的解決方案。 （記得，很多人認為一個個地載​​入小文件是很低效的，我們將在下一篇文章理介紹如何打包模塊）

除了異步加載以外，AMD的另一個優點是你可以在模塊裡使用對象、函數、構造函數、字符串、JSON或者別的數據類型，而CommonJS只支持對象。

再補充一點，AMD不支持Node裡的一些諸如 IO,文件系統等其他服務器端的功能。另外語法上寫起來也比CommonJS麻煩一些。

### UMD

在一些同時需要AMD和CommonJS功能的項目中，你需要使用另一種規範：Universal Module Definition（通用模塊定義規範）。

UMD創造了一種同時使用兩種規範的方法，並且也支持全局變量定義。所以UMD的模塊可以同時在客戶端和服務端使用。

下面是一個解釋其功能的例子：

```javascript=
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
      // AMD
    define(['myModule', 'myOtherModule'], factory);
  } else if (typeof exports === 'object') {
      // CommonJS
    module.exports = factory(require('myModule'), require('myOtherModule'));
  } else {
    // Browser globals (Note: root is window)
    root.returnExports = factory(root.myModule, root.myOtherModule);
  }
}(this, function (myModule, myOtherModule) {
  // Methods
  function notHelloOrGoodbye(){}; // A private method
  function hello(){}; // A public method because it's returned (see below)
  function goodbye(){}; // A public method because it's returned (see below)

  // Exposed public methods
  return {
      hello: hello,
      goodbye: goodbye
  }
}));
```

更多有關UMD的例子請看其Github上的官方[repo][4].

### 原生JS

你可能注意到了，上述的這幾種方法都不是JS原生支持的。要么是通過模塊模式來模擬，要么是使用CommonJS或AMD.

幸運的是在JS的最新規範ECMAScript 6 (ES6)中，引入了模塊功能。

ES6 的模塊功能汲取了CommonJS 和 AMD 的優點，擁有簡潔的語法並支持異步加載，並且還有其他諸多更好的支持。

我最喜歡的ES6 模塊功能的特性是，導入是實時只讀的。 （CommonJS 只是相當於把導出的代碼複製過來）。

來看例子：

```javascript=
// lib/counter.js

var counter = 1;

function increment() {
  counter++;
}

function decrement() {
  counter--;
}

module.exports = {
  counter: counter,
  increment: increment,
  decrement: decrement
};


// src/main.js

var counter = require('../../lib/counter');

counter.increment();
console.log(counter.counter); // 1
```

上面這個例子中，我們一共創建了兩份模塊的實例，一個在導出的時候，一個在引入的時候。

在 main.js 當中的實例是和原本模塊完全不相干的。這也就解釋了為什麼調用了 counter.increment() 之後仍然返回1。因為我們引入的 counter 變量和模塊裡的是兩個不同的實例。

所以調用 counter.increment() 方法只會改變模塊中的 counter .想要修改引入的 counter 只有手動一下啦：

```javascript=
counter.counter++;
console.log(counter.counter); // 2
```

而通過 import 語句，可以引入實時只讀的模塊：

```javascript=
// lib/counter.js
export let counter = 1;

export function increment() {
  counter++;
}

export function decrement() {
  counter--;
}


// src/main.js
import * as counter from '../../counter';

console.log(counter.counter); // 1
counter.increment();
console.log(counter.counter); // 2
```

這看起來很酷不是麼？這樣就實現了我們把模塊分隔在不同的文件裡，需要的時候又可以合併在一起而且不影響它的功能。

## 期待下一篇：[模塊打包][5]

我想看到這裡的你應該已經對JavaScript模塊化有了進一步的了解。

在下一篇教程裡，我們會介紹：

 - 為什麼要打包模塊
 - 幾種不同的打包構建方式
 - ECMAScript 模塊載入API





# Reference
> 原文鏈接：[JavaScript Modules: A Beginner’s Guide][50] 
> 作者：[Preethi Kasireddy][51]


[1]: http://web.jobbole.com/82520/
[2]: http://www.oschina.net/translate/javascript-module-pattern-in-depth
[3]: http://www.cnblogs.com/TomXu/archive/2011/12/30/2288372.html
[4]: https://github.com/umdjs/umd
[5]: https://hackmd.io/AwTgHMCsIMYIwFoDsKkICwDMQCMEEMdNMEATAJlwDYBTG4SkGoA=
[50]: https://medium.freecodecamp.com/javascript-modules-a-beginner-s-guide-783f7d7a5fcc
[51]: https://medium.freecodecamp.com/who-says-engineers-cant-be-entrepreneurs-8c7f7a6834da
