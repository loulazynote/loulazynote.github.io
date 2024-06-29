# Single Sign On with Kerberos

## Keberos 概述

Kerberos 是一種<span style="color:red;">**計算機網絡認證協議**</span>
它允許某實體在非安全網絡環境下通信
向另一個實體以一種安全的方式證明自己的身份
Kerberos 伺服器本身稱為<span style="color:red;">**鑰匙分配中心或 KDC**</span>

> _一般來說，KDC 除了發放票據與金鑰之外，同時也負責身份驗證的功能 (Authentication Server, AS)_

Kerberos 使用 **[Needham-Schroeder](#Needham_Schroeder)** 協議作為基礎

它使用一個由兩個獨立的邏輯部分：
<span style="color:red;">**認證伺服器**</span>
<span style="color:red;">**票據授權伺服器**</span>
組成"可信賴的第三方"
Kerberos 工作在用於證明用戶身份的"票據"基礎上

### <span id="Needham_Schroeder">Needham-Schroeder</span>

Needham 與 Schroeder 於 1978 年提出一種<span style="color:red;">**『多重盤問與回應』**</span>的認證協定，它不但可以避免重播攻擊，也可以解決相互認證的問題。

而協議的安全主要依賴於參加者對<span style="color:red;">**時間的鬆散同步**</span>和<span style="color:red;">**短周期**</span>的叫做 Kerberos 票據的認證聲明

下面為 Kerberos 內容名詞縮寫及解釋：

- <font size=3>[AS](#AS)（Authentication Server）: 認證伺服器</font>
- <font size=3>[KDC](#KDC)（Key Distribution Center）: 鑰匙分配中心</font>
- <font size=3>[TGS](#TGS)（Ticket Granting Server）: 票據授權伺服器</font>
- <font size=3>[TGT](#TGT)（Ticket Granting Ticket）: 票據授權票據，票據的票據</font>
- <font size=3>[ST](#ST)（Service Ticket）: 服務門票</font>
- <font size=3>[SS](#SS)（Service Server）: 特定服務提供端</font>

### <span id="AS">認證伺服器（Authentication Server/AS）</span>

相當於『[鑰匙分配中心](#KDC)』（KDC）
<span style="color:blue;">**管理每一個使用者的主密鑰（或稱共享密鑰）**</span>
欲加入的使用者都必須向 AS 伺服器申請帳戶並取得主密鑰
網路上任何一個使用者登入系統時，都會向認證中心取得通行證（Pass Book 或 TGT 票），有了通行證便可以在網路上索取所要的資源

> 簡單來說就是 驗證 Client 端的身份（確定你是身份證上的本人）
> 驗證通過就會給一張票證授予票證（[Ticket Granting Ticket/TGT](#TGT)）給 Client

### <span id="KDC">鑰匙分配中心(Key Distribution Center/KDC)</span>

<span style="color:blue;">**KDC 是一台伺服器，它與每位註冊的使用者分享不同的的私密對稱金鑰，當使用者第一次使用註冊時，這支金鑰可以利用人工輸入到伺服器**</span>
KDC 知道每位使用者的私密金鑰，而每位使用者可以使用此金鑰與 KDC 進行安全通訊

以 Star 星狀方式配置，從中心集中管理對外分散的 Key
採用 KDC 的管理方式可以減少 Key 的數量，它將所有的 Key 集中到一個中心點因此降降低了 Key 的數目，n 個 node 只需要 n 把 Key 即可，以 5 個點的拓樸系統來看只需要 5 把 Key，因此大大的降低系統對 Key 的需求

<br>

### <span id="TGS">票據授權伺服器（Ticket Granting Server/TGS）</span>

<span style="color:blue;">**TGS 管理網路上所有服務伺服器，並紀錄所有服務伺服器的秘密鑰匙**</span>
當有新的伺服器加入或退出時，都必須向 TGS 伺服器申請
而且某一秘密鑰匙也只有 TGS 伺服器和該服務伺服器兩者所擁有，因此 TGS 伺服器必須管理網路上所有伺服器的秘密鑰匙
使用者如要存取服務伺服器上資源時，必須向 TGS 提出它的身份證明，即 TGT 門票。
TGT 門票是經過 TGS 伺服器的秘密鑰匙加密，因此只有 TGS 伺服器能觀察門票的內容，別人無法仿冒
TGS 伺服器驗證完使用者的通行證後，再發給使用者有關使用者本身和所欲要求的服務伺服器的『服務門票』（Service Ticket, ST）
有了 ST 門票之後，使用者才可以到服務伺服器上存取資源，ST 門票同時包含了會議鑰匙

<br>

### <span id="TGT">票據授權票據，票據的票據（Ticket Granting Ticket/TGT）</span>

此票是由 AS 伺服器發給客戶端的身分證明使用；使用者可以持此票向 TGS 伺服器申請通往某一伺服器的請求
<span style="color:blue;">**Kerberos 協議將 TGT 的使用設計成可以避免經常向用戶詢問密碼（Kerberos 用來派生主密鑰的密碼）或將主密鑰存儲在工作站上**</span>

<br>

### <span id="ST">服務門票（Service Ticket/ST）</span>

當使用者出示 TGT 門票，向 TGS 伺服器要求前往某一伺服器
如果 TGS 伺服器同意其要求時，則發給所要求伺服器的 ST 門票給使用者，使用者持此票即可要求該伺服器提供服務

> _ST（Service Ticket）也有資料稱為 TGS Ticket_

### Keberos 流程關係

![](https://i.imgur.com/gTQCCOp.png)

消息 A：使用 Client/User 的秘密密鑰加密的 Client/TGS Session Key。
消息 B：使用 TGS 的密鑰加密的票證授予票證。
消息 C：由消息 B 的 TGT 和所請求服務的 ID 組成。
消息 D：使用 Client/TGS 會話密鑰加密的身份驗證器。
消息 E：使用服務的秘密密鑰加密的 Client 到 Server 票證。
消息 F：使用 Client/TGS Session Key 加密的 Client/Server Session Key。
消息 G：一個新的身份驗證器，其中包括 Client ID，時間戳記，並使用 Client/Server Session Key 進行了加密。
消息 H：在 Client 的身份驗證器中找到的時間戳已使用 Client/Server Session Key 進行了加密。

## 名詞解釋

### Principal

Kerberos principal（又稱為主體）用於在 kerberos 加密系統中標記一個唯一的身份
主體可以是用戶（如 louis）或服務（如 namenode 或 hive）

根據約定，主體名稱分為三個部分：
主名稱、實例和領域。
例如，典型的 Kerberos 主體可以是 louis/admin@EXAMPLE.COM

### keytab

> _keytab 是包含 principals 和加密 principal key 的文件_ > _keytab 用於將 SSO 帶到未加入 AD domain 的應用程序_

keytab 文件對於每個 host 是唯一的，因為 key 中包含 hostname
keytab 文件用於不需要人工交互和保存純文本密碼，實現到 kerberos 上驗證一個主機上的 principal

因為 Server 上可以訪問 keytab 文件即可以以 principal 的身份通過 kerberos 的認證，所以，keytab 文件應該被妥善保存，應該只有少數的用戶可以訪問

**如何產生 Keytab?**
使用實用程序 KTPASS.EXE
它是 RSAT 的一部分，如果啟用了 Active Directory 目錄服務工具將顯示在 RSAT 中
`ktpass /out app1.keytab /princ http/app1.verenatex.com@verenatex.com /mapuser app1 /crypto AES256-SHA1 /ptype KRB5_NT_PRINCIPAL /pass Password2 /target vdc01.verenatex.com`

- `/out` 指定輸出文件的名稱，如 app1.keytab
- `/princ` 指定 userPrincipalName 以及將其添加到對映帳戶的 servicePrincipalName 的值
- `/mapuser` 是將為其生成 Keytab 的帳戶
- `/crypto` 是用於 Keytab 的加密類型，此處為 AES256-SHA1
- `/ptype` 通常應使用 KRB5_NT_PRINCIPAL，除非應用程序文檔另有說明
- `/pass` 是對應帳戶的新密碼（默認情況下，它將執行密碼重置操作）
- `/target` 用於指示將查詢哪個 domain 控制器(it is optional, but can be useful if you have more than one domain in your forest)

## Single Sign-On (SSO)

單一登錄（SSO）協議（Kerberos，SAML，OpenID 等）允許用戶使用單個 ID 和密碼來訪問不同的應用程序。用戶登錄以獲得對連接系統的訪問權限，或者使用輕型目錄訪問協議（LDAP）和（目錄）服務器上存儲的 LDAP 數據庫來完成訪問。

## SPNEGO

SPNEGO 代表"**簡單和受保護的 GSS_API 協商機制**"，用於擴展基於 Kerberos 的 SSO 環境以用於 Web Application，因為 SPNEGO 被設計用於 Client Server 桌面環境，通常不用於 Web Application 或簡易 Client 環境

> _Spnego 是一種與身份驗證器協商使用哪種安全協議的機制
> 例如 Kerberos，NTLM，摘要或 Basic.5t6_

## SPN(Service Principal Names)

<span style="color:red;">**服務主體名稱 (SPN) 是服務實例的唯一標識符**</span>
Kerberos 身份驗證使用 SPN 將服務實例與服務登錄帳戶關聯
這允許用戶端應用程式請求服務對帳戶進行身份驗證，即使客戶端沒有帳戶名稱

如果在整個林中的電腦上安裝多個服務實例，則每個實例必須有自己的 SPN
如果用戶端可能使用多個名稱進行身份驗證，則給定的服務實例可以有多個 SPN
例如，SPN 始終包含運行服務實例的主機的名稱，因此服務實例可能會為其主機的每個名稱或別名註冊 SPN

在 Kerberos 身份驗證服務可以使用 SPN 對服務進行身份驗證之前，必須在服務實例用於登錄的帳戶物件上註冊 SPN
給定的 SPN 只能在一個帳戶上註冊
對於 Win32 服務，服務安裝程式在安裝服務實例時指定登錄帳戶
然後，安裝程式組成 SPN，並將它們作為活動目錄域服務中的帳戶物件的屬性編寫
如果服務實例的登錄帳戶發生更改，則必須在新帳戶下重新註冊 SPN

當用戶端想要連接到服務時，它會查找服務的實例，為該實例編寫 SPN，連接到該服務，並顯示用於服務的 SPN 進行身份驗證

<br>

![](https://i.imgur.com/0gGQ9mK.png)

1. Client 上的 Application（例如 Browser）嘗試訪問 Web Server 上的受保護頁面
2. Server 響應要求授權
3. Application 從 Kerberos KDC 請求 Service ticket
4. 獲得 ticket 後，Application 將其包裝為 SPNEGO 格式的請求，然後通過 Browser 將其發送回 Web Application
5. 執行已部署的 Web Application 的 web container（Tomcat、JBoss EAP 等）對請求進行解包並驗證 ticket
6. 認證成功後，將授予訪問權限

## Browser 如何透過 Kerberos 進行身分驗證

### 循序圖

![](https://i.imgur.com/kQ523BD.png)

1. User 登錄 Windows，並通過 KDC 進行了身份驗證(對於 Windows，KDC 將是主域控制器)
2. OS 為 User 接收一個 TGT token
3. 當 User 嘗試連接到 Tomcat 服務器(或其他 web container)時，將<span style="color:red">協商身份驗證機制</span>(紅字原文: _the authentication mechanism is negotiated_)
4. User 的 token 被傳遞給 Tomcat，然後 Tomcat 通過 KDC 對其進行驗證
5. 驗證 User 身份後，Tomcat 隨後從 LDAP 服務器中檢索其角色<br>（在 Windows 中為 Active Directory，這是一種專門處理諸如用戶名和密碼之類的身份信息的數據庫）
6. 並確定他是否有權訪問（Response 200 OK）他在服務器上請求的資源。

## 參考資料

1. [Setting up Kerberos Authentication for a Website in IIS](https://tinyurl.com/y47k7wke)
2. [Single Sign On with Kerberos](https://tinyurl.com/y6abgv4q)
3. [Kerberos Wikipedia](https://tinyurl.com/y3n46j89)
4. [鳥哥的 Linux 私房菜](https://tinyurl.com/y56quglw)
5. [看完您如果還不明白 Kerberos 原理，算我輸](https://tinyurl.com/yyvyntg3)
6. [翻轉工作室-資訊與網路安全技術：第十四章 Kerberos 認證系統](https://tinyurl.com/y393eqc5)
7. [The Secret Security Wiki](https://tinyurl.com/y697kz84)
8. [Kerberos.NET](https://github.com/dotnet/Kerberos.NET)
9. [Guide To Setup Kerberos Single Sign-On (SSO)](https://tinyurl.com/y3cuh2nh)
10. [瞭解 keytab 需求](https://tinyurl.com/y35xoqex)
11. [宅學習](https://tinyurl.com/y5adlxz5)
12. [如何使用 Spn，當您在 [網際網路資訊服務上設定 裝載的 Web 應用程式]](https://tinyurl.com/y6ydnysx)
13. [Microsoft Developer-Service Principal Names](https://tinyurl.com/yxs9ox83)
14. [瞭解 keytab 需求](https://tinyurl.com/y35xoqex)
15. [All you need to know about Keytab files](https://tinyurl.com/y2f3yp82)

