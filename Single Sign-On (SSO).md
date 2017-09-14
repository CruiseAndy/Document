# Single Sign-On (SSO) (單一登入/一鍵登入)

單一登入（英語：Single sign-on，縮寫為 SSO），又譯為單一簽入，一種對於許多相互關連，但是又是各自獨立的軟體系統，提供存取控制的屬性。當擁有這項屬性時，當使用者登入時，就可以取得所有系統的存取權限，不用對每個單一系統都逐一登入。這項功能通常是以[輕型目錄訪問協議][1]（LDAP）來實作，在伺服器上會將使用者資訊儲存到LDAP資料庫中。相同的，單一登出（single sign-off）就是指，只需要單一的登出動作，就可以結束對於多個系統的存取權限。目前SSO有三種方式，OpenID & SAML & OAuth

### 優點
 - 降低訪問第三方網站風險（用戶密碼不存儲或外部管理）。
 - 從不同的用戶名和密碼的組合減少密碼疲勞。
 - 減少花費的時間重新輸入密碼相同的身份。
 - 降低IT成本適當降低一些IT幫助台呼叫有關密碼。
 - SSO集中的所有其他應用程序和系統，用於身份驗證服務器的身份驗證，並與技術相結合是為了確保用戶不必主動輸入憑據一次以上。

# OpenID
OpenID是一個去中心化的網上身分認證系統。對於支援OpenID的網站，用戶不需要記住像使用者名稱和密碼這樣的傳統驗證標記。取而代之的是，他們只需要預先在一個作為OpenID身分提供者（identity provider, IdP）的網站上註冊。OpenID是去中心化的，任何網站都可以使用OpenID來作為用戶登入的一種方式，任何網站也都可以作為OpenID身分提供者。OpenID既解決了問題而又不需要依賴於中心性的網站來確認數位身分。
OpenID正在被越來越多的大網站採用，比如作為身分提供者的`AOL`和`Orange`。OpenID 可以和 .NET Framework的Windows CardSpace一起使用。

### OpenID相關基本術語
 - 終端使用者（End User）：想要向某個網站表明身分的人。
 - 標識（Identifier）：終端使用者用以標識其身分的URL或[XRI][2]。
 - 身分提供者（Identity Provider, IdP）：提供OpenID URL或XRI註冊和驗證服務的服務提供者。
 - 依賴方（Relying Party, RP）：想要對終端使用者的標識進行驗證的網站。

### 登入
一個想要為其存取者提供OpenID登入網站，比如`example.com`，需要在頁面的某個地方放置一個登入表單。與提示用戶輸入使用者名稱和密碼的傳統登入表單不同的是，這種表單只有一個輸入框：OpenID標識。網站可以選擇在這個輸入框旁顯示一個小的OpenID圖示。這個表單與OpenID用戶端庫的實現相連線。
假設用戶Alice在身分提供者openid-provider.org處註冊了一個OpenID標識：`alice.openid-provider.org`。如果Alice想使用這個標識來登入`example.com`，她只需要到example.com去將alice.openid-provider.org填入OpenID登入表單。
如果標識是一個URL，依賴方example.com做的第一件事情是將這個URL轉換為典型格式，如：`http://alice.openid-provider.org/`。在OpenID 1.0中，依賴方接著請求該URL對應的網頁，然後通過一個HTML連結tag發現提供者伺服器，比如`http://openid-provider.org/openid-auth.php`。同時也確定是否應該使用授權身分（delegated identity）。從OpenID 2.0開始，依賴方請求的是XRDS文件（也稱為Yadis文件），使用內容類型application/xrds+xml。這種類型在URL中可能存在，而在XRI中總是存在。
依賴方可以通過兩種模式來與身分提供者通訊：
 - checkid_immediate：兩個伺服器間的所有通訊都在後台進行，不提示用戶。
 - checkid_setup：用戶使用存取依賴方站點的同一個瀏覽器窗口與身分提供者伺服器互動。

第二種模式更加常用。而且，如果操作不能夠自動進行的話，checkid_immediate模式會轉換為checkid_setup模式。
首先，依賴方與身分提供者建立一個「共享秘密」——一個聯繫控制代碼，然後依賴方儲存它。如果使用checkid_setup模式，依賴方將用戶的網頁瀏覽器重新導向到提供者。在這個例子裡，Alice的瀏覽器被重新導向到`openid-provider.org`，這樣Alice能夠向提供者驗證自己。
驗證的方法可能不同，但典型地，OpenID提供者要求提供密碼（然後可能使用cookies儲存用戶對談，就像很多基於密碼驗證的網站的做法一樣）。如果Alice當前沒有登入到`openid-provider.org`，她可能被提示輸入密碼，然後被問到是否信任依賴方頁面——如`http://example.com/openid-return.php`，這個頁面被example.com指定為用戶驗證完成後返回的頁面——取得她的身分資訊。如果她給出肯定回答，OpenID驗證被認為是成功的，瀏覽器被重新導向到被信任的返回頁面。如果Alice給出否定回答，瀏覽器仍然會被重新導向，然而，依賴方被告知它的請求被拒絕，所以example.com也依此拒絕Alice的登入。
但是，登入的過程還沒有結束，因為在這個階段，`example.com`不能夠確定收到的資訊是否來自於`openid-provider.org`。如果他們之前建立了「共享秘密」，依賴方就可以用它來驗證收到的資訊。這樣一個依賴方被稱為是stateful的，因為它儲存了對談間的「共享秘密」。作為對比，stateless的驗證方必須再作一次背景請求（check_authentication）來確保資料的確來自`openid-provider.org`。
Alice的標識被驗證之後，她被看作以`alice.openid-provider.org`登入到`example.com`。接著，這個站點可以儲存這次對談，或者，如果這是她的第一次登入，提示她輸入一些專門針對example.com的資訊，以完成註冊。
OpenID不提供它自己的驗證方式，但是如果身分提供者使用強驗證，OpenID可被用於安全事務，比如銀行和電子商務。

# SAML
安全斷言標記式語言（英語：Security Assertion Markup Language，簡稱SAML，發音sam-el）是一個基於XML的開源標準資料格式，它在當事方之間交換身分驗證和授權資料，尤其是在身分提供者和服務提供者之間交換。SAML是OASIS安全服務技術委員會的一個產品，始於2001年。其最近的主要更新發布於2005年，但協定的增強仍在通過附加的可選標準穩步增加。
SAML解決的最重要的需求是網頁瀏覽器單一登入（SSO）。單點登入在企業網路層面比較常見，（例如使用Cookie），但將其擴充功能到企業網路之外則一直存在問題，並使得不可互操作的專有技術激增。（另一種近日解決瀏覽器單點登入問題的方法是OpenID Connect協定）

### 原則
SAML規範定義了三個角色：委託人（通常為一名用戶）、身分提供者（IdP），服務提供者（SP）。在用SAML解決的使用案例中，委託人從服務提供者那裡請求一項服務。服務提供者請求身分提供者並從那裡並獲得一個身分斷言。服務提供者可以基於這一斷言進行存取控制的判斷——即決定委託人是否有權執行某些服務。
在將身分斷言傳送給服務提供者之前，身分提供者也可能向委託人要求一些資訊——例如使用者名稱和密碼，以驗證委託人的身分。SAML規範了三方之間的斷言，尤其是斷言身分訊息是由身分提供者傳遞給服務提供者。在SAML中，一個身分提供者可能提供SAML斷言給許多服務提供者。同樣的，一個服務提供者可以依賴並信任許多獨立的身分提供者的斷言。
SAML沒有規定身分提供者的身分驗證方法；他們大多使用使用者名稱和密碼，但也有其他驗證方式，包括採用多重要素驗證。諸如輕型目錄存取協定、RADIUS和Active Directory等目錄服務允許用戶使用一組使用者名稱和密碼登入，這是身分提供者使用身分驗證令牌的一個典型來源。許多流行的網際網路社群網路服務也提供身分驗證服務，理論上他們也可以支援SAML交換。

### 設計
SAML 建立在一些現有標準之上：
 - Extensible Markup Language (XML)：大多數SAML交換是以一個標準化的XML方言表示，這也是SAML的名稱（Security Assertion Markup Language）的根源。; XML Schema (XSD): SAML斷言和協定部分採用[XML Schema][3]。
 - XML Signature：SAML 1.1和SAML 2.0都為身分驗證和訊息完整性使用基於XML Signature標準的數位簽章。
 - XML Encryption：SAML 2.0使用XML Encryption為加密名稱識別元、加密屬性和加密斷言提供元素（SAML 1.1沒有加密功能）。但XML加密據報有著嚴重的安全問題。
 - Hypertext Transfer Protocol (HTTP)：SAML很大程度上依賴[超文字傳輸協定][4]作為其通訊協定。
 - SOAP：SAML指定使用SOAP，尤其是SOAP 1.1



# OAuth
OAuth（開放授權）是一個開放標準，允許用戶讓第三方應用存取該用戶在某一網站上儲存的私密的資源（如相片，影片，聯絡人列表），而無需將使用者名稱和密碼提供給第三方應用。
OAuth允許用戶提供一個令牌，而不是使用者名稱和密碼來存取他們存放在特定服務提供者的資料。每一個令牌授權一個特定的網站（例如，影片編輯網站)在特定的時段（例如，接下來的2小時內）內存取特定的資源（例如僅僅是某一相簿中的影片）。這樣，OAuth讓用戶可以授權第三方網站存取他們儲存在另外服務提供者的某些特定資訊，而非所有內容。
OAuth是OpenID的一個補充，但是完全不同的服務。

### OAuth 2.0
OAuth 2.0是OAuth協定的下一版本，但不向下相容OAuth 1.0。OAuth 2.0關注用戶端開發者的簡易性，同時為Web應用，桌面應用和手機，和起居室裝置提供專門的認證流程。
Facebook的新的Graph API只支援OAuth 2.0，Google在2011年3月也宣布Google API對OAuth 2.0的支援，Windows Live也支援OAuth 2.0。

---

:::info
## [OpeniD & SAML & OAuth different][5]
:::




# Reference
### [Single Sign-On Wiki][50]
### [Microsoft Azure SSO][51]
### [OpenID Wiki][52]
### [SAML Wiki][53]
### [OAth Wiki][54]



[1]: https://zh.wikipedia.org/wiki/%E8%BD%BB%E5%9E%8B%E7%9B%AE%E5%BD%95%E8%AE%BF%E9%97%AE%E5%8D%8F%E8%AE%AE
[2]: https://zh.wikipedia.org/wiki/XRI
[3]: https://zh.wikipedia.org/wiki/XML_Schema
[4]: https://zh.wikipedia.org/wiki/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE
[5]: https://spin.atomicobject.com/2016/05/30/openid-oauth-saml/
[50]: https://zh.wikipedia.org/wiki/%E5%96%AE%E4%B8%80%E7%99%BB%E5%85%A5
[51]: https://msdn.microsoft.com/zh-tw/library/azure/hh967643.aspx
[52]: https://zh.wikipedia.org/wiki/OpenID
[53]: https://zh.wikipedia.org/wiki/%E5%AE%89%E5%85%A8%E6%96%AD%E8%A8%80%E6%A0%87%E8%AE%B0%E8%AF%AD%E8%A8%80
[54]: https://zh.wikipedia.org/wiki/OAuth
