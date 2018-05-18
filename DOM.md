# DOM

## 為什麼說操作DOM很耗效能

首先，DOM對象本身也是一個js對象，所以嚴格來說，並不是操作這個對象慢，而是說操作了這個對象後，會觸發一些瀏覽器行為，比如布局（layout）和繪製（paint）。下面主要先介紹下這些瀏覽器行為，闡述一個頁面是怎麼最終被呈現出來的，另外還會從代碼的角度，來說明一些不好的實踐以及一些優化方案。
![](https://i.imgur.com/5Bfvdsz.png)

## 為什麼會發展出React.js

在很久很久以前，差不多 2010 年時，Facebook 的開發團隊發現他們渲染頁面的引擎越來越不堪負荷。
可以想像的是，由於功能越多，觸發頁面的事件越多，前端資料狀態的變化也越頻繁，因此改變 DOM 結構也會越頻繁。我想你應該要知道的是 HTML DOM 是一個很大的樹狀資料，每當一個事件發生，而需要進行新增、修改、刪除時，底層的程式就必須先從這一棵大樹中，一直深入，再深入，直到找到某一片你有興趣的葉子，可想而知這是多麽大的工程！
而另外一個問題是，他們發現前端的程式越來越難維護，我想應該是因為由事件觸發的程式，很難進行封裝和模組化。就像你用某一個 jQuery 套件時，你可能需要先在你的 HTML 上加入相對應的 class 或 id 才行，而到了程式非常複雜的時候，你根本不知道這一個 class 到底背後的行為是什麼，而且也會患有不敢修改的恐懼症！

所以，為了要解決

1. 頻繁改變 DOM 是很耗效能的
2. 前端程式很難封裝和模組化

React 就由此產生了，而他的母親是 FB 的工程師 Jordan Walke。
2011 年時，React 開始實作來解決上述兩個議題。
2013 年時，Instagram 使用 React 進行改版，也在同年，React 在 Github 上開放程式碼。

## Virtual DOM

我們知道改變 DOM 是很耗效能的一件事，而一律重繪將會改變所有的 DOM，但是如果開發者跟 DOM 中間有一個人，可以幫我們改變最小幅度的 DOM 那該有多好。
這個人就是 Virtual DOM，一律重繪改的事實上是記憶體中的 DOM 物件，而 React 會在根據舊狀態和新狀態的排版和資料進行比對，將確切有需要更動的 DOM 真實反映在瀏覽器的 DOM 上。
這種比對和效能的調校不會是你感興趣的，React 幫你做了。你只需要了解你應用程式的排版是怎麼樣就好了。

Virtual DOM 算法主要是实现三個步骤：element，diff，patch。然后就可以实际的进行使用

# Reference
[為什麼說DOM操作很慢][1]
[2016 年の前端，瘋什麼 ReactJS？][2]
[浏览器的工作原理：新式网络浏览器幕后揭秘][3]
[網上都說操作真實 DOM 慢，但測試結果卻比 React 更快，為什麼？][4]
[为什么说 DOM 绑定事件很耗性能?][5]
[DOM是什麼？][6]
[關於文件物件模型(DOM)][7]


[1]: https://read01.com/LD6y6.html
[2]: https://medium.com/4cats-io/2016-%E5%B9%B4%E3%81%AE%E5%89%8D%E7%AB%AF-%E7%98%8B%E4%BB%80%E9%BA%BC-reactjs-4727a6ecc85a
[3]: https://www.html5rocks.com/zh/tutorials/internals/howbrowserswork/#Painting
[4]: https://www.yuntui.org/topics/xqrbb/answers/oezyv
[5]: https://www.zhihu.com/question/54966411/answer/142326882
[6]: http://karenten10-blog.logdown.com/posts/190835-what-is-the-dom
[7]: https://moztw.org/docs/gecko/aboutdom/

