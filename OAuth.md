# OAuth 2.0

## OAuth 2.0 的角色定義
- **Resource Owner** \- 可以授權別人去存取 Protected Resource 。如果這個角色是人類的話，則就是指使用者 (end-user)。
- **Resource Server** \- 存放 Protected Resource 的伺服器，可以根據 Access Token 來接受 Protected Resource 的請求。
- **Client** \- 代表 Resource Owner 去存取 Protected Resource 的應用程式。 “Client” 一詞並不指任何特定的實作方式（可以在 Server 上面跑、在一般電腦上跑、或是在其他的設備）。
- **Authorization Server** \- 在認證過 Resource Owner 並且 Resource Owner 許可之後，核發 Access Token 的伺服器。

## 基本流程概觀與資料定義
![hello](images/abstract%20protocol%20flow.png)

## 四種內建授權流程 (Grant Flows)
- **Authorization Code Grant Type Flow**
- **Implicit Grant Type Flow**
- **Resource Owner Password Credentials Grant Type Flow**
- **Client Credentials Grant Type Flow**
