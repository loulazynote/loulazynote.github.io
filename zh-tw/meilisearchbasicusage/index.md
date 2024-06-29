# MeiliSearch基本用法


## MeiliSearch(網稱 美麗搜尋)

### 前言

讓我玩了接近4個月的套件, 讓我踩雷滿滿的套件, 還要應付User腦洞大開的奇幻冒險, 怎麼可以不留下記錄呢?<br/>
但相比其他幾套知名的開源全文檢索套件來說, 這套真的比較輕量也比較簡單

### 何為MeiliSearch

MeiliSearch 是一個輕量級的現成全文檢索套件, 使用`RESTful`搜尋API即可使用, 並支援各大系統(Windows、MacOS、Linux), 且是 **Open Source**所以不必擔心商業授權問題
而官方也有提供 [`MeiliSearch Cloud`](https://www.meilisearch.com/pricing) 的付費方案, 讓其使用者可以不用架設自己的Server環境也能享有全文檢索的功能

至於為何檔案這麼小, 卻又很快速呢?<br/>
據官方文件說明, MeiliSearch在第一次啟動的時候就會自動建立Database, 其檔案在根目錄的`data.ms`的檔案夾內, 而這類Database引擎則是 [`Lightning Memory-Mapped Database(LMDB)`](http://www.lmdb.tech/doc/), 官方認為`LMDB`是性能、穩定性和特性的最佳組合

> - 可以在建立時使用`Cli`指令去更改DB的存放路徑 `-db-path`, 或使用Docker建立
> - `LMDB`是用C語言撰寫的事物型鍵值儲存，是為OpenLDAP開發的，具有ACID屬性

### 有那些功能?

- `Typo tolerance` 錯字容錯率
- `Custom ranking` 自定義搜尋時關聯性及先後順序
- `Stop words` 停用字
- `Highlighting` 搜尋結果中凸顯的搜索關鍵字
- `Synonyms` 同義詞
- `Filtering` 篩選器
- `Sorting` 將搜尋結果排序
- `Search preview` 簡單的預覽頁面

    > 還有更細部的功能, 之後我只會講到我用過的功能

### 基本使用方式

#### 安裝方式(以Windows為例)

1. 透過`cURL`安裝

   ```sh
        # Install Meilisearch

        curl -L <https://install.meilisearch.com> | sh

        # Launch Meilisearch

        ./meilisearch
   ```

2. 使用 Docker 安裝(我個人是比較推薦這種方式,因為比較方便)

    ```dockerfile
        # Fetch the latest version of Meilisearch image from DockerHub
        docker pull getmeili/meilisearch:v1.0

        # Launch Meilisearch in development mode with a master key
        docker run -it --rm \
            -p 7700:7700 \
            -e MEILI_ENV='development' \
            -v $(pwd)/meili_data:/meili_data \
            getmeili/meilisearch:v1.0

    ```

3. 將Source Code Clone下來自行編譯(可能需要額外安裝 `Rust` , `cargo` 等語言或套件)

    ```sh

        git clone https://github.com/meilisearch/meilisearch
        cd meilisearch

        git checkout latest

        # Update the rust toolchain to the latest version
        rustup update

        # Compile the project
        cargo build --release

        # Execute the server binary
        ./target/release/meilisearch

    ```

4. 下載官方已打包好的[執行檔](https://github.com/meilisearch/meilisearch/releases/latest)

   選擇好想要放的路徑及位置即可

    ![meilisearch.exe](https://user-images.githubusercontent.com/33840759/220128851-accd4785-cb83-4923-9a3b-cc6992aa6577.png)

   - 建議要建立的硬體設備或是虛擬環境 至少要 <span style="color:red">**2 cores/Ram 16G 以上**</span> 會較順暢
  
     因為如果沒有額外設定的話, 預設Ram的使用率是該設備的2/3可用的RAM 及 1/2 可用的線程

     > 曾經我就是用了Azure VM 最低配置去跑, 結果每1.2天就要重開執行檔, 3~5天要重開機一次

5. 也支援各種[雲端服務](https://docs.meilisearch.com/learn/getting_started/installation.html#cloud-deploy)
   - AWS
   - Azure
   - DigitalOcean
   - GCP
   - Koyeb
   - Qovery
   - Railway

> 注意!! 如果曾經用過此套件 版本為 `0.X.XX` 版的, 這裡 `1.0.0` 版本沒辦法直接用喔, 因為他們有說大版更會 Breaking changes, 而下一次則是在`2.0.0`版本的時候
> ![image](https://user-images.githubusercontent.com/33840759/220133045-cb83a535-69f8-4fc6-b497-d0de2f57bdd6.png)
>
> [解決方法](https://docs.meilisearch.com/learn/advanced/updating.html)則是匯出所有Database內的資料再匯入新版本的

#### 使用方法(以執行檔為例)

1. 到放執行檔的File內後直接執行

   ```pwsh
      cd D:/MeiliSearchDemo
      
      .\meilisearch-windows-amd64.exe
   ```

   首次執行會自動產生Database相關檔案<br/>

    ![image](https://user-images.githubusercontent.com/33840759/220134718-7084cef2-5ad7-41dc-bcfb-611afe3bc5c9.png)

   而`1.0.0`版本開始 如果沒有加 `Master Key` 它則會提醒, 並提供一組雜湊值
   ![image](https://user-images.githubusercontent.com/33840759/220135220-1904c75d-1348-40c7-b455-a47fe2e63975.png)

    > 建議都使用 `Master Key` 來保護自行設定的MeiliSearch

    ```pwsh
      cd D:/MeiliSearchDemo
      
      # `--master-key`是設定密鑰的CLI
      .\meilisearch-windows-amd64.exe --master-key="MASTER_KEY"
   ```

   執行完成後, MeiliSearch就開始Run起來了,預設的Port都是7700,且MeiliSearch有提供簡易預覽可以到`localhost:7700`查看
   > 想要更改port及Http位址的話, 需要使用到 `--http-addr` 的CLI<br/>
   > `.\meilisearch-windows-amd64.exe --master-key="MASTER_KEY" --http-addr="127.0.0.1:2023"`

2. 增加索引內容
   1. 在需要使用的專案上, 開啟nuget搜尋`Meilisearch`或打開Terminal鍵入

      ```pwsh
        dotnet add package Meilisearch
      ```

   1. 先建立想要索引的欄位Model(這些欄位是可以自定義的)

        ```CSharp
            public class Movie
            {
                // 如果有設定Id這個欄位,則MeiliSearch會自動判斷為PKey,有多個的話則不會,可以自行指定要那個欄位為PKey
                public int Id { get; set; }
                public string Title { get; set; }
                public string Poster { get; set; }
                public string Overview { get; set; }
                public IEnumerable<string> Genres { get; set; }
            }
        ```

   1. 增加索引的方法

       ```CSharp
            public async Task CreateDocumentAsync()
            {
                var options = new JsonSerializerOptions
                {
                    PropertyNameCaseInsensitive = true
                };
                string jsonString = await File.ReadAllTextAsync("Models/Database/movies.json");
                var movies = JsonSerializer.Deserialize<IEnumerable<Movie>>(jsonString, options);
                // 如果index沒有建立過,在此會自動建立起來,如果已有此index,則會讀取相關內容
                var index = client.Index("movies");
                // 此方法即是增加Document,也就是增加要被索引的內容
                await index.AddDocumentsAsync(movies);
            }
       ```

3. 如何搜索

    ```CSharp

        public async Task QueryAsync(string? keyword)
        {
            // 獲取要搜尋的Index索引
            var index = client.Index("movies");
            // 使用SearchAsync方法來搜尋鍵入的關鍵字
            var movies = await index.SearchAsync<Movie>(keyword);
            // 搜尋與索引內容相關的都在Hits內, 而上一層有其他欄位(EX: query => 輸入的關鍵字, limit => 最多獲取的筆數, offset => 跳過的筆數)
            foreach (var movie in movies.Hits)
            {
                Console.WriteLine(movie.Title);
            }
        }

    ```

4. 完整範例

   ```CSharp
        public class Program
        {
            public MeilisearchClient client = new MeilisearchClient("http://127.0.0.1:7700", "jdWLTzfPHZK46YB8aQOXEPPD8RrPkU0E2505ixbMco8");
            public static void Main(string[] args)
            {
                var p = new Program();
                Console.WriteLine("Wait for create Document...");
                p.CreateDocumentAsync().Wait();
                Console.WriteLine("Input Your Search Keyword:");
                var keyword = Console.ReadLine();
                Console.WriteLine("=================================");
                p.QueryAsync(keyword).Wait();
                Console.ReadKey();
            }

            public async Task CreateDocumentAsync()
            {
                var options = new JsonSerializerOptions
                {
                    PropertyNameCaseInsensitive = true
                };
                string jsonString = await File.ReadAllTextAsync("Models/Database/movies.json");
                var movies = JsonSerializer.Deserialize<IEnumerable<Movie>>(jsonString, options);
                // 如果index沒有建立過,在此會自動建立起來,如果已有此index,則會讀取相關內容
                var index = client.Index("movies");
                // 此方法即是增加Document,也就是增加要被索引的內容
                await index.AddDocumentsAsync(movies);
                // 取得當前新增後的Task,裡面會有當前狀態
                var task = await client.GetTaskAsync(0);
                Console.WriteLine("Create Result:" + task.Status);
            }

            public async Task QueryAsync(string? keyword)
            {
                // 獲取要搜尋的Index索引
                var index = client.Index("movies");
                // 使用SearchAsync方法來搜尋鍵入的關鍵字
                var movies = await index.SearchAsync<Movie>(keyword);
                // 搜尋與索引內容相關的都在Hits內, 而上一層有其他欄位(EX: query => 輸入的關鍵字, limit => 最多獲取的筆數, offset => 跳過的筆數)
                foreach (var movie in movies.Hits)
                {
                    Console.WriteLine(movie.Title);
                }
            }
        }

   ```

    程式執行結果
    ![image](https://user-images.githubusercontent.com/33840759/223657985-c390d3bb-74c7-40ef-8334-a027c170f308.png)

    MeiliSearch提供的簡易Demo結果
    ![image](https://user-images.githubusercontent.com/33840759/223658581-80ce0314-5d22-44ee-9278-65a5dda4698c.png)

## 參考資料

1. [Meilisearch Documentation v1.0](https://docs.meilisearch.com/learn/what_is_meilisearch/overview.html)
2. [Meilisearch Github](https://github.com/meilisearch)

