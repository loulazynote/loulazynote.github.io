# SeriLog 設定之眉角


## Installation

1. 用 NuGet 搜尋 `Serilog.AspNetCore`安裝套件
   ![image](https://user-images.githubusercontent.com/33840759/177329564-b5a40318-2dac-4198-8308-da1751f2fce8.png)

1. 在 `Program.cs` 加入設定
   ![image](https://user-images.githubusercontent.com/33840759/133586930-a45ee783-cb57-4b13-a7a7-7c1e376a02f7.png)

   ```cs
   Log.Logger = new LoggerConfiguration()
           .MinimumLevel.Override("Microsoft", LogEventLevel.Warning)
           .MinimumLevel.Override("System", LogEventLevel.Warning)//使用MinimumLevel 來設定 LogLevel 層級,如果來源為 Microsoft 及 系統的訊息 則LogLevel 層級為 警告
           .WriteTo.Console(new RenderedCompactJsonFormatter())
           .WriteTo.File(new CompactJsonFormatter().ToString())//Output templates, 輸出至Console及檔案
           .Enrich.FromLogContext()
           .CreateLogger();
   ```

   > `CompactJsonFormatter` 將保留消息模板、屬性和格式信息，以便稍後可以創建呈現的消息。當 JSON 旨在在沒有消息模板呈現的環境中使用時，可以改用 `RenderedCompactJsonFormatter`。

   ![image](https://user-images.githubusercontent.com/33840759/133590800-8d9a8ca5-5331-40ff-89c1-b9f54268ed93.png)
   `loggerConfiguration.ReadFrom.Configuration(hostingContext.Configuration)` : 讀取現在的`Configuration`

1. `appsetting.json` 加入設定
   ![image](https://user-images.githubusercontent.com/33840759/133589515-29a327c2-f30e-4853-9cb7-52ce4b74037f.png)

   - `Microsoft.Hosting.Lifetime` : 應用程式生命週期事件的通知
   - `Microsoft.EntityFrameworkCore.Database.Command` : 紀錄 EF Core 自動產生的 SQL 命令

1. `Startup.cs` 設定
   ![image](https://user-images.githubusercontent.com/33840759/133591350-7ed75a24-714f-49ee-acc7-9cfea26737d5.png)

   ```cs
   app.UseSerilogRequestLogging(options =>
            {
                // Customize the message template
                options.MessageTemplate = "Handled {RequestPath}";

                // Emit debug-level events instead of the defaults
                options.GetLevel = (httpContext, elapsed, ex) => LogEventLevel.Debug;

                // Attach additional properties to the request completion event
                options.EnrichDiagnosticContext = (diagnosticContext, httpContext) =>
                {
                    diagnosticContext.Set("RequestHost", httpContext.Request.Host.Value);
                    diagnosticContext.Set("RequestScheme", httpContext.Request.Scheme);
                };
            });
   ```

## Usage

1. 在需要的地方加入 log
   1. 先 using `Microsoft.Extensions.Logging`
   2. 加入建構式
      ![image](https://user-images.githubusercontent.com/33840759/133592365-f6999f05-a23d-46a9-b4c6-92aa37d59464.png)
   3. 在需要 log 的部分加入 log 記錄
      ![image](https://user-images.githubusercontent.com/33840759/133592580-aa25b116-f21f-4315-a60c-ff8b54ba8a77.png)
      > 可以自訂訊息等級
      > ![image](https://user-images.githubusercontent.com/33840759/133593069-74b65aef-44bc-4cd8-946f-0edb50a0a159.png)
1. 因在`Program.cs`及`appsetting.json`有加入設定,其餘 System 的操作及 Database 的讀取皆會被 log 下來
   ![image](https://user-images.githubusercontent.com/33840759/177325685-4459ae33-4eba-446f-843d-8dfa075e401a.png)

   ![image](https://user-images.githubusercontent.com/33840759/177326054-00631a19-c92d-468c-b060-acb5aab39591.png)

## 參考資料

1. [Github - serilog-aspnetcore](https://tinyurl.com/yfq24pmk)
2. [Github - Serilog.Formatting.Compact](https://tinyurl.com/yj4kvqgu)
3. [Microsoft - .NET Core 與 ASP.NET Core 中的記錄](https://tinyurl.com/yffdxzrq)
4. [WilL Blog - ASP.NET Core 如何紀錄 Entity Framework Core 5.0 自動產生的 SQL 命令](https://tinyurl.com/yjsjsmdh)
5. [m@rcus 學習筆記 - [NETCore] 結構化日誌 Serilog - 配置設定](https://tinyurl.com/y3ujh2vf)
6. [C# Corner - How To Implement Logging Using Serilog In ASP.NET Core 5.0 Application With Database](https://tinyurl.com/yej7xtfu)

