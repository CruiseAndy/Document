# Flux.js

FLUX是一個由Facebook提出來的開發架構 ==(FLUX 是一個 Pattern 而不是一個正式的框架)==，目的是在解決所謂的MVC在大型商業網站所存在的問題，把沒有條理跟亂七八糟的架構做一個流程規範的定義。

#### FLUX希望作到的事情

 - Use explicit data instead of derived data
 - Separate data from view state

#### FLUX希望帶來的好處

 - Improved data consistency
 - Easier to pinpoint root of a bug
 - More meaingful unit tests


## MVC與遇到的問題

![MVC](http://i.imgur.com/ypxMX8H.png)

上面這是一個MVC架構所定義的流程與各流程的內容, 我們將一個網頁程式分為三項組成的要素:

 - Controller
 - Model
 - View

MVC普遍被大家用在目前的網站開發專案當中，因為它簡單方便的幫我們分別定義了各層需要異動的地方，把關注點隔離開來(separation of concerns，SOC)。但隨這著專案的Controller,Model與View變多沒有限制的流程也讓他產生了一些問題。

![MVC](http://i.imgur.com/RSYGVoi.png)

而上面是一個複雜的MVC流程，這也是比較貼近我們實務上大家遇到的架構

比較起來很快就發現了一點，當我們功能越多，提供的Controller, Model, View關聯愈複雜時，往往會忘了哪一個連去哪一個，這會讓我們的架構定義的越來越發散，久了就不知道什麼才是正確的架構，而這也會讓我們難以維護跟修改，甚至擴大了測試範圍...。

傳統的雙向資料綁定會造成連鎖更新，不容易去預測一個單一互動所造成的改變結果，這也是為什麼擴大了測試範圍的原因。 


## FLUX介紹

接著我們先來介紹FLUX的中心思想打造單一的資料流進行方式(one-way data flow)，相對於MVC只定義了三個角色的功能與關係，FLUX更明確的定義了一個資料進行的方式，使得大家更容易遵守規則。

 - Actions : Helper methods that facilitate passing data to the Dispatcher
 - Dispatcher : Receives actions and broadcasts payloads to registered callbacks
 - Stores : Containers for application state & logic that have callbacks registered to the dispatcher
 - Controller Views&View : React Components that grab the state from Stores and pass it down via props to child components.

這邊FLUX使用了Dispatcher這樣的一個唯一物件(singleton)來管理一到多個Store，而每個Store可對應一個View的概念來提供呈現所需資料，而當需要增加view的互動功能時，則透過向Dispatcher註冊Action的方式來達到事件的觸發。 整個資料的處理流程就改變為下面的樣貌：

![](http://i.imgur.com/KPI6yOS.png)

實作上，程式運作的流程如下：使用者在View上面操作後，View會依據使用者不同的行為，建立起不同的Action送給Dispatcher。因為Stores中 已經針對不同的Action建立起不同的處理function並Register到Dispatcher，所以Dispatcher會依據這些 Action執行Stores給予的邏輯。Stores處理完內部的狀態後，就會觸發Change事件。Controller View會Listen這些事件，知道Stores內部狀態修改後，透過Stores API取得變動後的資料，並將這些資料透過this.state傳給Controller View下層的React Component。

這邊要注意，因為FLUX的大前提是建立一個單一的資料進行方式，所以非常不建議為了view的顯示而跳過步驟直接修改Store，這樣又會讓架構跟先前的MVC提的遇到一樣不明確的問題。

### Store

Store主要是在管理React Component運作的狀態資料，不同的Action發生時，進行狀態資料的更新處理及邏輯運算。例如一個計算機Component，他的Store要處理的事情，就是當Clean的Action發生時，就要進行資料值的歸零。除此之外，當狀態資料有異動時，也要產生一個事件，以通知相關的View。

由下圖可以看出，Store其實是這整個模式的中心，他透過Dispatcher的Register()將處理不同Action的邏輯注入到Dispatcher中。當不同的Action發生，Store會被通知，並且執行相對應的邏輯。這樣子的資料流是由Action流到Dispatcher再到Store。
 
當Store的邏輯進行時，會引發Change事件，而監聽(Listen)這個Change事件的角色，就會是Controller View。關注此Store的View，如果需要處理該Store的變動，則會建立一個事件處理的function到此Store的異動事件上。這樣子的資料流是由Store到View。並且讓View自行決定如何處理Store的異動事件，如果View需要知道狀態資料的內容，則再呼叫Store給的API以取得回傳值。

![](http://i.imgur.com/yBNyE8E.png)

### Dispatcher

Dispatcher：處理View到Action再到Store間的資料流。Dispatcher是Singleton，統一處理Action的派送。Dispatcher的機制是讓Stroes登錄(Register) callback function。該function中會處理該Store有興趣處理的Action。所以在之後由View觸發的Action中，會傳入Payload(資料) 到Dispatcher中，Dispatcher自行去叫用對應的Store callback function。這種設計的好處是把Store與Action的相依性分開，Action不用去管是哪個Store要來處理它。當一個Action被定義出來後，要增加或減少Stores的處理都沒關係。而Store對於Action的關係也是單向的，Store並不會反向去影響到Action。

![](http://i.imgur.com/qzqN6mx.png)

### Controller

Controller View：Controller View的目的是作為其子元件的State管理中心，統一管理整個子孫代的View的資料，以及統一處理Store傳來的Change事件。當資料異動時，會接收到Store的發出的事件通知，Controller View再自行決定該如何處理此資料異動，並透過props傳遞給子元件。Controller View通常不會產生Action，主要是接受Store傳來的資料流，並傳到底下的子View。而Action主要是那些子孫View透過UI的互動產生，然後產生另一個由Action到Store到Controller View的循環。

![](http://i.imgur.com/fpAz33w.png)







# Reference
[FLUX架構介紹與實作FLUX架構][50]
[What is Flux?][51]
[React.JS學習筆記(4) - Flux簡介][52]
[Flux - 為React打造的單向資料流架構][53]

[50]: https://dotblogs.com.tw/blackie1019/2015/04/14/151049
[51]: http://fluxxor.com/what-is-flux.html#what-is-flux-
[52]: https://dotblogs.com.tw/lapland/2015/07/13/151850
[53]: http://eddychang.me/blog/javascript/94-flux-concept.html


