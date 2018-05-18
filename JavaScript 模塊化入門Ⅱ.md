# JavaScript 模塊化入門Ⅱ：模塊打包建構

在[上一篇教程][1]裡我們已經討論過什麼是模塊，為什麼要使用模塊以及多種實現模塊化的方式。

這次，我們會聊一聊什麼是模塊打包，為什麼要打包模塊，模塊打包的方式工具，還有它當前在Web開發中的運用。

## 什麼是模塊打包？

粗俗一點來講，模塊打包就是把一小坨一小坨的代碼粘成一大坨。

實際操作起來的時候當然還需要關註一些細節。

## 為什麼要打包模塊？

一般來講，我們用模塊化組織代碼的時候，都會把模塊劃分在不同的文件和文件夾裡，也可能會包含一些諸如React和Underscore一類的第三方庫。

而後，所有的這些模塊都需要通過<script>標籤引入到你的HTML文件中，然後用戶在訪問你網頁的時候它才能正常顯示和工作。每個獨立的<script>標籤都意味著，它們要被瀏覽器分別一個個地加載。

這就有可能導致==頁面載入時間過長==。

為了解決這個問題，我們就需要進行模塊打包，把所有的模塊==合併到一個或幾個文件==中，以此來==減少HTTP請求數==。這也可以被稱作是從開發到上線前的構建環節。

還有一種提升加載速度的做法叫做代碼壓縮（混淆）。其實就是去除代碼中不必要的空格、註釋、換行符一類的字符，來保證在不影響代碼正常工作的情況下壓縮其體積。

更小的文件體積也就意味著更短的加載時間。要是你仔細對比過帶有 .min後綴的例如 jquery.min.js和jquery.js的話，應該會發現壓縮版的文件相較之下要小很多。

![](http://i.imgur.com/L7qSDVj.png)

Gulp和Grunt一類的構建工具可以很方便地解決上述的需求，在開發的時候通過模塊來組織代碼，上線時再合併壓縮提供給瀏覽器。

## 打包模塊的方法有哪些？
如果你的代碼是通過之前介紹過的模塊模式來組織的，合併和壓縮它們其實就只是把一些原生的JS代碼合在一起而已。

但如果你使用的是一些瀏覽器原生不支持的模塊系統（例如CommonJS 或AMD，以及ES6 模塊的支持現在也不完整），你就需要使用一些專門的構建工具來把它們轉換成瀏覽器支持的代碼。這類工具就是我們最近經常聽說的Browserify, RequireJS, Webpack等等模塊化構建、模塊化加載工具了。

為了實現模塊化構建或載入的功能，這類工具提供許多諸如在你改動源代碼後自動重新構建（文件監聽）等一系列的功能。

下面我們就一起來看一些實際的例子吧：

## 打包 CommonJS
在上一篇教程中我們了解到， CommonJS是同步載入模塊的，這對瀏覽器來說不是很理想。其實下面介紹的模塊化構建工具Browserify在上一篇也提到過。它是一個專門用來打包CommonJS模塊以便在瀏覽器裡運行的構建工具。

舉個例子，假如你在 main.js 文件中引入了一個用來計算平均數的功能模塊：

```javascript=
var myDependency = require('myDependency');

var myGrades = [93, 95, 88, 0, 91];

var myAverageGrade = myDependency.average(myGrades);
```

在這個示例中，我們只有一個名為 myDependency 的模塊依賴。通過下面的命令，Browserify會依次把main.js裡引入的所有模塊一同打包到一個名為 bundle.js 的文件裡：

> browserify main.js -o bundle.js

Browserify 首先會通過==抽象語法樹==（[AST][2]）來解析代碼中的每一個 require 語句，在分析完所有模塊的依賴和結構之後，就會把所有的代碼合併到一個文件中。然後你在HTML文件裡引入一個bundle.js就夠啦。

多個文件和多個依賴也只需要再稍微配置一下就能正常工作了。

之後你也可以使用一些例如==Minify-JS==的工具來壓縮代碼。

## 打包 AMD
假若你使用的是AMD，你會需要一些例如RequireJS 或 Curl的AMD加載器。模塊化加載工具可以在你的應用中按需加載模塊代碼。

需要再次提醒一下，AMD 和 CommonJS 的最主要區別是AMD是異步加載模塊的。這也就意味著你不是必須把所有的代碼打包到一個文件裡，模塊加載不影響後續語句執行，逐步加載的的模塊也不會導致頁面阻塞無法響應。

不過在實際應用中，為了避免用戶過多的請求對服務器造成壓力。大多數的開發者還是選擇用RequireJS optimizer, r.js一類的構建工具來合併和壓縮AMD的模塊。

總的來說，AMD 和 CommonJS 在構建中最大的區別是，在開發過程中，採用AMD的應用直到正式上線發布之前都不需要構建。

要是你對CommonJS vs. AMD的討論感興趣，可以看這一篇[AMD is Not the Answer][3]

## Webpack
Webpack 是新推出的構建工具裡最受歡迎的。它兼容CommonJS, AMD, ES6各類規範。

也許你會質疑，我們已經有這麼多諸如Browserify 或 RequireJS 的工具了，為什麼還需要 Webpack 呢？究其原因之一，Webpack 提供許多例如 code splitting（代碼分割） 的有用功能，它可以把你的代碼分割成一個個的 chunk 然後按需加載優化性能。

舉個例子，要是你的Web應用中的一些代碼只在很少的情況下才會被用到，把它們全都打包到一個文件裡是很低效的做法。所以我們就需要 code splitting 這樣的功能來實現按需加載。而不是把那些很少人才會用到的代碼一股腦兒全都下載到客戶端去。

code splitting 只是 Webpack 提供的眾多強大功能之一。當然，網上也為這些模塊化構建工具吵得不可開交。你要是感興趣的話也可以在下面這些地方觀摩一下：

 - [https://gist.github.com/substack/68f8d502be42d5cd4942][4]
 - [Browserify vs. Webpack][5]
 - [Browserify VS Webpack][6]

## ES6 模塊

看他們吵夠了的話，接下來我就要介紹一下ES6模塊了。假如你採用ES6模塊，在不遠的將來對那些構建工具的需求可能會小一些。首先我們還是看看ES6模塊是怎麼加載的吧。

ES6模塊和CommonJS, AMD一類規範最主要的區別是，當你載入一個模塊時，載入的操作實際實在編譯時執行的——也就是在代碼執行之前。所以去掉那些不必要的exports導出語句可以優化我們應用的性能。

有一個經常會被問到的問題：去除exports和冗餘代碼消除（UglifyJS一類工具執行後的效果）之間有什麼區別？

答案是這個要具體情況具體分析，感興趣的話可以上Github看這個Repo：[Rollup’s wiki][7]

讓ES6模塊與冗餘代碼消除（Dead code elimination）不同的是一種叫做tree shaking的技術。 Tree shaking其實恰好是冗餘代碼消除的反向操作。它只加載你需要調用的代碼，而不是刪掉不會被執行的代碼。我們還是用一個具體的例子說明吧：

假設我們有如下一個使用ES6語法，名為 utils.js 的函數：

```javascript=
export function each(collection, iterator) {
  if (Array.isArray(collection)) {
    for (var i = 0; i < collection.length; i++) {
      iterator(collection[i], i, collection);
    }
  } else {
    for (var key in collection) {
      iterator(collection[key], key, collection);
    }
  }
 }

export function filter(collection, test) {
  var filtered = [];
  each(collection, function(item) {
    if (test(item)) {
      filtered.push(item);
    }
  });
  return filtered;
}

export function map(collection, iterator) {
  var mapped = [];
  each(collection, function(value, key, collection) {
    mapped.push(iterator(value));
  });
  return mapped;
}

export function reduce(collection, iterator, accumulator) {
    var startingValueMissing = accumulator === undefined;

    each(collection, function(item) {
      if(startingValueMissing) {
        accumulator = item;
        startingValueMissing = false;
      } else {
        accumulator = iterator(accumulator, item);
      }
    });

    return accumulator;
}
```

現在我們也不清楚到底需要這個函數的哪些功能，所以先全部引入到 main.js 中：

```javascript=
//main.js
import * as Utils from './utils.js';
```

之後我們再調用一下 each 函數：

```javascript=
//main.js
import * as Utils from './utils.js';

Utils.each([1, 2, 3], function(x) { console.log(x) });
```

通過 "tree shaken" 之後的 main.js 看起來就像下面這樣：

```javascript=
//treeshake.js 
function each(collection, iterator) {
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

each([1, 2, 3], function(x) { console.log(x) });
```

注意到這裡只導出了我們調用過的 each 方法。

再如果我們只調用 filter 方法的話：

```javascript=
//main.js
import * as Utils from './utils.js';

Utils.filter([1, 2, 3], function(x) { return x === 2 });
```

"Tree shaken" 之後就會變成這樣：

```javascript=
function each(collection, iterator) {
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

function filter(collection, test) {
  var filtered = [];
  //注意在filter中调用了each，所以两个方法都会被引入
  each(collection, function(item) {
    if (test(item)) {
      filtered.push(item);
    }
  });
  return filtered;
};

filter([1, 2, 3], function(x) { return x === 2 });
```

構建ES6模塊

现在我们已经了解到ES6模块载入的与众不同了，但我们还没有聊到底该怎么构建ES6模块。

因为浏览器对ES6模块的原生支持还不够完善，所以现阶段还需要我们做一些补充工作。

让ES6模块在浏览器中顺利运行的常用方法有以下几种：

1. 使用语法编译器（Babel或Traceur）来把ES6语法的代码编译成ES5或者CommonJS, AMD, UMD等其他形式。然后再通过Browserify 或 Webpack 一类的构建工具来进行构建。

2. 使用[Rollup.js][8]，这其实和上面差不多，只是Rollup还会捎带的利用“tree shaking”技术来优化你的代码。在构建ES6模块时Rollup优于Browserify或Webpack的也正是这一点，它打包出来的文件体积会更小。Rollup也可以把你的代码转换成包括ES6, CommonJS, AMD, UMD, IIFE在内的各种格式。其中IIFE和UMD可以直接在浏览器里运行，AMD, CommonJS, ES6等还需要你通过Browserify, Webpack, RequireJS一类的工具才能在浏览器中使用。

## 小心踩坑

這裡有一些坑還需要和大家說明一下。轉換語法優雅的ES6代碼以便在瀏覽器裡運行並不是一件令人舒爽的事情。

問題在於，什麼時候我們才能免去這些多餘的工作。

令人感動的答案是：“差不多快了。”

ECMAScript目前包含一個名為[ECMAScript 6 module loader API][6] 的解決方案。簡單來講，這個解決方案允許你動態加載模塊並緩存。還是來舉例說明：

**myModule.js**

```javascript=
export class myModule {
  constructor() {
    console.log('Hello, I am a module');
  }

  hello() {
    console.log('hello!');
  }

  goodbye() {
    console.log('goodbye!');
  }
}
```

**main.js**

```javascript=
System.import('myModule').then(function(myModule) {
  new myModule.hello();
});

// ‘Hello!, I am a module!’
```

同樣，你可以在script標籤上設置type=module的屬性來直接定義模塊：

```javascript=
<script type="module">
  // loads the 'myModule' export from 'mymodule.js'
  import { hello } from 'mymodule';
  new Hello();
</script>
 // ‘Hello!, I am a module!’
```

更加详细的介绍也可以在Github上查看：[es6-module-loader][9]

## 我們已經有了原生ES6模塊，還需要那些亂七八糟的玩意兒麼？

越來越多的人使用ES6模塊產生了一些有趣的影響：

HTTP/2 出現之後，模塊化構建工具是不是都該被淘汰了？

在HTTP/1中，一次TCP連接只允許一個請求，所以我們需要通過減少載入的文件數來優化性能。而HTTP/2改變了這一切，請求和響應可以並行，一次連接也允許多個請求。

每次請求的消耗也會遠遠小於HTTP/1，所以載入一堆模塊就不再是一個影響性能的問題了。所以許多人認為打包模塊完全就是多餘的了。這聽起來很合理，但我們也需要具體情況具體分析。

其中有一條，模塊化構建解決了一些HTTP/2解決不了的問題。例如去除冗餘的代碼以壓縮體積。要是你開發的是一個對性能要求很高的網站，模塊化構建從長遠上考慮會給你帶來更多好處。當然，要是你不那麼在意性能問題，以後完全就可以省卻這些煩人的步驟了。

總之，我們離所有的網站都採用HTTP/2傳輸還有相當一段時間。短期內模塊化構建還是很有必要的。

要是你對HTTP/2的其他特性也感興趣，可以查閱這裡：[HTTP/2][10]

**CommonJS , AMD, UMD這類標準會過時麼？**

一旦ES6成為了模塊化的標準，我們還需要這些非原生的東西麼？

這點還值得商榷。

在JavaScript中採用統一標準，通過import和export來使用模塊，省略所有繁雜的多餘步驟確實很爽。不過到底要多久ES6才能成為真正的模塊化標準呢？

反正不會很快。

並且開發者也有各自的偏好，“唯一的解決方案”永遠也不會存在。





# Reference
> 原文链接：[JavaScript Modules Part 2: Module Bundling][50]

> 作者：[Preethi Kasireddy][51]


[1]: https://hackmd.io/AwRghmAcBmwGwFppwOwFYEBYBMYCcCekKAJggKaYklwDGJkx5eQA
[2]: https://en.wikipedia.org/wiki/Abstract_syntax_tree
[3]: http://tomdale.net/2012/01/amd-is-not-the-answer/
[4]: https://gist.github.com/substack/68f8d502be42d5cd4942
[5]: https://mattdesl.svbtle.com/browserify-vs-webpack
[6]: http://blog.namangoel.com/browserify-vs-webpack-js-drama
[7]: https://github.com/rollup/rollup
[8]: https://rollupjs.org/
[9]: https://github.com/ModuleLoader/es-module-loader
[10]: https://http2.github.io/faq/#what-are-the-key-differences-to-http1x
[50]: https://medium.freecodecamp.com/javascript-modules-part-2-module-bundling-5020383cf306
[51]: https://medium.freecodecamp.com/who-says-engineers-cant-be-entrepreneurs-8c7f7a6834da




