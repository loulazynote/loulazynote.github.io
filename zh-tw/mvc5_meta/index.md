# MVC 5 filter

## Filter

> <span style="color:red;">*Filter 的作用是在 Action 執行前或執行後做一些加工處理*</span>

+ Authorization Filter：
Authorization 是五種 Filter 中優先序最高的，通常用於驗證 Request 合不合法，不合法後面就直接跳過。
+ Resource Filter：
Resource 是第二優先，會在 Authorization 之後，Model Binding 之前執行。通常會是需要對 Model 加工處裡才用。
+ Action Filter：
最容易使用的 Filter，封包進出都會經過它，使用上沒什麼需要特別注意的。跟 Resource Filter 很類似，但並不會經過 Model Binding。
+ Exception Filter：
異常處理的 Exception。
+ Result Filter：
當 Action 完成後，最終會經過的 Filter。

### 運作方式

ASP .NET Core 的每個 Request 都會先經過已註冊的 Middleware 接著才會執行 Filter，除了會依照上述的順序外，同類型的 Filter 預設都會以先進後出的方式處裡封包。
Response 在某些 Filter 並不會做處理，會值接 Bypass。Request 及 Response 的運作流程如下圖：
![](https://i.imgur.com/MBK4jZ5.png)

+ 黃色箭頭是正常情況流程
+ 灰色箭頭是異常處理流程

## 參考資料

+ [John Wu's [鐵人賽 Day14] ASP.NET Core 2 系列 - Filters](https://tinyurl.com/y4ghf4qm)

