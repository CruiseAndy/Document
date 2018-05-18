# AMD

## 什麼是AMD?

AMD的英文全名是Asynchronous Module Definition，是Common JS所定義的一組優雅且簡單API，

用於同步或非同步載入Javascript Module和它所依賴的其他Module，

而Require JS就是一套很著名且已經實作這組API的Library。

它特別適合使用於解決瀏覽器使用同步(Synchronous)載入所導致的效能、可使用性、測試和Cross Domain等問題。

AMD的API主要格式如下

```
define(id?, dependencies?, factory);
```

 - id 代表Module的名稱
 - dependencies 代表Module所依賴的其他Module
 - factory 代表產生Module的工廠(Factory)或是它的實體(Object)


## 為什麼要使用AMD?

而為什麼要使用AMD呢? 其實主要原因可以從它的名字來了解

### Asynchronous

在瀏覽器中，如果我們直接使用Script標籤載入JS檔案，

瀏覽器會按照順序讀取並且馬上執行，這可能會造成頁面讀取時發生Block的現象，

而如果我們在設置Script標籤時並未依照相依性的順序來擺放，也可能會造成JS的執行發生錯誤。

而透過非同步載入Javascript Module機制，可以讓瀏覽器載入網頁時不會因為載入的Javascript而導致載入緩慢，

而是使用Parallel的方式載入JS Module，讓網頁讀取速度更快。

也可以透過管理Module之間的相依性，讓網頁載入真正需要依賴的Module，

而不是無條件通通將Module載入。

在這邊提醒大家一個觀念，如果不是必要且被依賴性重的JS Library(例如JQuery)，
不要放在Head中，而是放在網頁的最底端載入

### Module

隨著網頁的複雜程度越來越高，在網頁之中所使用的Javascript也越來越多，

所以我們可以透過將JS程式碼模組化，來讓網頁的設計也可以符合OOP的精神，

不但方便管理使用，也可以達到關注點分離(Seperation of Concerns)，讓你的網頁程式更好維護

### Definition

良好的定義和標準化，可以讓程式所能使用的範圍和可維護性更高，

而Common JS則是一組由自願者所組成的團體，他們定義出了一套Javascript API的標準、規範和建議，

讓Javascript的應用可以走出瀏覽器，甚至是開發一般的應用程式(如node.js)。


[Ref](https://dotblogs.com.tw/kirkchen/2012/06/20/javascript_amd_introduction)
