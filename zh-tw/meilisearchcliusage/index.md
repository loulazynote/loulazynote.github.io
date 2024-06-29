# MeiliSearch 常用Cli 設定


## MeiliSearch 常用Cli 設定

### 前言

關於MeiliSearch有一些在使用前可以做的設定, 官方也提供多種方式都與安裝方式有關, 這篇就記錄一下這些設定要怎麼使用(主要以Cli方式)

### 有哪些指令?

#### 基本Cli

##### `--db-path` : 指定DB建立的位置

- 環境變數: `MEILI_DB_PATH`

  ```pwsh
    ./meilisearch-windows-amd64.exe --db-path='D:/MeiliSearchDemo/Database'
  ```

##### `--http-addr` : 指定Http位址及Port號

- 環境變數: `MEILI_HTTP_ADDR`

  ```pwsh
    ./meilisearch-windows-amd64.exe --http-addr='127.0.0.1:7700'
  ```

##### `--env` : 指定開發環境或是正式環境

- 環境變數: `MEILI_ENV`

  ```pwsh
    ./meilisearch-windows-amd64.exe --env=development
  ```

- `production` : 設定為正式環境會有一些限制, 必須要設定 `master key`, 且套件提供的簡易預覽畫面會關閉
- `development` : `master key`是可選項目不一定要設定, 套件提供的簡易預覽畫面開啟

##### `--master-key` : 設定密鑰, 至少要設定16個UTF-8的字元, 預設是沒有設定的, 如果啟動時沒有設定密鑰, 或低於16字元, 那Meilisearch會自動產生一個"建議"的密鑰, 可以在重新啟動時將它寫入

- 環境變數: `MEILI_MASTER_KEY`

   > 密鑰保護除了 [`GET /health`](https://docs.meilisearch.com/reference/api/health.html) 的API以外都包含在內

    ![image](https://user-images.githubusercontent.com/33840759/220135220-1904c75d-1348-40c7-b455-a47fe2e63975.png)

    ```pwsh
       ./meilisearch-windows-amd64.exe --master-key="MASTER_KEY"
    ```

##### `--no-analytics` : 關閉分析, Meilisearch會自動收集實例的資料, 所有收集的資料只用於改進Meilisearch, 可以使用此指令關閉

- 環境變數: `MEILI_NO_ANALYTICS`

    ```pwsh
        ./meilisearch-windows-amd64.exe --no-analytics
    ```

##### `--dump-dir` : 設定位置以產生DB的轉移dump文件, 可用於恢復Meilisearch資料或在不同版本之間[轉移](https://docs.meilisearch.com/learn/advanced/updating.html)

- 環境變數: `MEILI_DUMP_DIR`
  
##### `--import-dump` : 匯入指定路徑的轉移dump文件, 如果Database已經存在, Meilisearch將拋出一個錯誤並中止啟動

- 環境變數: `MEILI_IMPORT_DUMP`
  
##### `--ignore-missing-dump` : 防止 Meil​​isearch 在 `--import-dump` 未指向有效轉移dump文件時跳出錯誤, 如果Database已經存在, 將正常啟動而不導入任何轉移dump文件

- 環境變數: `MEILI_IGNORE_MISSING_DUMP`

##### `--ignore-dump-if-db-exists` : 使用 `--import-dump` 時防止Database已經存在 而Meil​​isearch 執行時跳出錯誤, 如果Database已經存在, 轉移dump文件將被忽略，Meilisearch 將使用現有Database啟動

- 環境變數: `MEILI_IGNORE_DUMP_IF_DB_EXISTS`
- 此指令不需要填寫任何值, 加上指令即可, 有加上值則會跳出錯誤
- 如果未定義 `--import-dump`此選項則跳錯誤

##### `--log-level` : 設定MeiliSearch的Log等級, 顯示在Dos畫面上 (聽說有被正名叫命令提示字元, 那powershell/bash/docker這些畫面不就也要另外名詞?我就懶)

- 環境變數: `MEILI_LOG_LEVEL`
- `INFO` : 預設值, 記錄所有事件
- `ERROR` : 只記錄Meilisearch的意外事件及錯誤
- `WARN` : 記錄所有意外事件,無論嚴重程度
- `DEBUG` : 記錄所有事件, 包括Meilisearch內部程式流的詳細訊息
- `TRACE` : 記錄所有事件，包括Meilisearch內部程式流的更多詳細信息, 官方不建議使用, 因為它顯示的內容太多太冗長, 官方更建議使用 `DEBUG`
- `OFF` : 關閉記錄
  
  ![image](https://user-images.githubusercontent.com/33840759/225888370-96ba628d-07f6-4238-9587-76b6e7a47c11.png)

#### 比較重要的Cli(請慎用)

##### `--max-indexing-memory` : 設定MeiliSearch在建立索引時可以使用的記憶體大小, 預設是使用該機器(PC/VM/Server一樣)不超過<span style="color:red;">**2/3的可用記憶體**</span>

- 環境變數: `MEILI_MAX_INDEXING_MEMORY`
- 此設定必須以字元為單位, 或明確的一個基本單位, 如: `107374182400` 或 `'107.7Gb'` 或 `'107374Mb'`等
- 如果設定大於或等於機器的總記憶體的值, 非常有可能導致實作崩潰

  > 在索引過程中, Meilisearch有可能超過設定的RAM限制, 但在大多數情況下, 這應該是可以忽略的量, 對穩定及性能幾乎沒有影響(官方是這樣說啦~但我感受是還是有影響)

    ```pwsh
      ./meilisearch-windows-amd64.exe --max-indexing-memory='4Gb'
    ```

##### `--max-indexing-threads` : 設定 Meil​​isearch 在建立索引時可使用的最大核心數, 預設是避免使用機器總處理單元的一半以上

- 環境變數: `MEILI_MAX_INDEXING_THREADS`
- 預設是 <span style="color:red;">**使用機器1/2可用內核數**</span>
- 如果 `--max-indexing-threads` 大於機器實際可用內核數, MeiliSearch將使用最大可用內核數
- 在單核機器上, MeiliSearch只能使用唯一可用的內核來進行索引, 這可能會導致建立索引時的搜索體驗下降 (親體驗用2 Cores也很慢)

  > 請避免將 `--max-indexing-threads` 設定為機器處理器內核的總數, 雖然這樣可能加快建立索引速度, 但可能會嚴重影響搜索體驗

    ```pwsh
      ./meilisearch-windows-amd64.exe --max-indexing-threads=4
    ```

##### `--http-payload-size-limit` : 限制Http Request的大小, 預設是100Mb,  如果超過設定值會拒絕Request並給予錯誤訊息

- 環境變數: `MEILI_HTTP_PAYLOAD_SIZE_LIMIT`
- 此設定必須以字元為單位, 或明確的一個基本單位, 如: `107374182400` 或 `'107.7Gb'` 或 `'107374Mb'`等

    ```pwsh
      ./meilisearch-windows-amd64.exe --http-payload-size-limit='200Mb'
    ```

#### 關於備份Cli(1.0.0版本後才有的功能)

##### `--schedule-snapshot` : 自動備份Database及index, 預設是關閉的, 而啟用的方式則是加上此指令即可

- 環境變數: `MEILI_SCHEDULE_SNAPSHOT`
- 可以直接使用, 並不指定數值, 這樣的話每24HR就會自動備份一次(`--schedule-snapshot=86400`)
- 提供的數值以秒計算

    ```pwsh
       # 這樣表示每一小時都備份一次
      ./meilisearch-windows-amd64.exe --schedule-snapshot=3600
    ```

   > 經測試, 備份是做差異備份, 如果資料都沒有變動, 則會有一個暫存檔案會變更
    ![image](https://user-images.githubusercontent.com/33840759/225876994-3221a4f2-5968-4f63-bdc5-1e9c41806416.png)

##### `--snapshot-dir` : 備份檔案指定位置, 預設是套件所在根目錄下的`snapshots/`

- 環境變數: `MEILI_SNAPSHOT_DIR`
  
##### `--import-snapshot` : 匯入備份

- 環境變數: `MEILI_IMPORT_SNAPSHOT`

    ```pwsh
      ./meilisearch-windows-amd64.exe --import-snapshot='D:/MeiliSearchDemo/snapshots/'
    ```

- 如果Database已存在則會失敗
- 找不到有效的備份檔案(snapshot)則會失敗

##### `--ignore-missing-snapshot` : 防止 Meil​​isearch 在 `--import-snapshot` 未指向有效快照文件時跳出錯誤, 如果未定義 `--import-snapshot`，此指令將跳出錯誤

- 環境變數: `MEILI_IGNORE_MISSING_SNAPSHOT`
- 不需要給值, 給的話會跳錯

##### `--ignore-snapshot-if-db-exists` : 使用 `--import-snapshot` 時防止已有資料庫的 Meil​​isearch 跳出錯誤，且snapshot將被忽略, MeiliSearch將使用現有資料庫啟動

- 環境變數: `MEILI_IGNORE_SNAPSHOT_IF_DB_EXISTS`
- 如果未定義 `--import-snapshot`, 此指令會跳錯誤
- 不需要給值, 給的話會跳錯

#### SSL選項Cli設定(預設值都是`none`)

##### `--ssl-auth-path` : 啟用客戶端身份驗證, 需要提供一個文件夾的路徑, MeiliSearch 會從該文件夾中讀取客戶端證書, 而該文件夾應包含一個或多個 PEM 格式的客戶端證書, 通常用於安全性要求較高的應用程序, 例如企業內部系統

- 環境變數: `MEILI_SSL_AUTH_PATH`

    ```pwsh
        # 需要將 --ssl-cert-path 及 --ssl-key-path 和 --http-addr 選項的值更改為自己的值
        ./meilisearch --http-addr '127.0.0.1:7700' --ssl-cert-path 'D:/Meilisearch/ssl/cert.pem' --ssl-key-path 'D:/Meilisearch/ssl/key.pem' --ssl-auth-path 'D:/Meilisearch/ssl/client_certs'
    ```

##### `--ssl-cert-path` : 指定 SSL/TLS 服務器證書的路徑, 內容包含 **公共證書的 PEM**的檔案, 通常由 CA 驗證機構簽發的 SSL/TLS 證書,該證書用於驗證 SSL/TLS 服務器的身份

- 環境變數: `MEILI_SSL_CERT_PATH`

##### `--ssl-key-path` : 設定SSL 密鑰文件, 其值必須是`PEM`格式的 `RSA` 私鑰或 `PKCS8` 編碼私鑰的路徑

- 環境變數: `MEILI_SSL_KEY_PATH`

    ```pwsh
        ./meilisearch --http-addr '127.0.0.1:7700' --ssl-cert-path 'D:/Meilisearch/ssl/cert.pem' --ssl-key-path 'D:/Meilisearch/ssl/key.pem'
    ```

#### SSL選項的以下指令我還沒試過(有經驗的人可以分享一下XD)

##### `--ssl-ocsp-path` : 設定 Server的OCSP檔案 (此選項非必要)

- 環境變數: `MEILI_SSL_OCSP_PATH`
- 從 OCSPFILE 讀取 DER 編碼的 OCSP Response並將其裝訂到證書

##### `--ssl-require-auth` : 讓SSL認證變成強制性的, 如果User沒有完成認證則會發出強烈的警告(就是警告)

- 環境變數: `MEILI_SSL_REQUIRE_AUTH`
- 不需要給任何值, 直接加上這個指令即可

##### `--ssl-resumption` : SSL 會話恢復

- 環境變數: `MEILI_SSL_RESUMPTION`
- 不需要給任何值, 直接加上這個指令即可

##### `--ssl-tickets` : 啟用 SSL/TLS 會話票證

- 環境變數: `MEILI_SSL_TICKETS`
- 不需要給任何值, 直接加上這個指令即可

#### 如果我設定很多很長很冗容易忘記怎麼辦?

##### `--config-file-path` : 指定啟動時要讀取的設定文件的位置

- 環境變數: `MEILI_CONFIG_FILE_PATH`
- 沒有設定的話, 會如下圖顯示

    ![image](https://user-images.githubusercontent.com/33840759/225889190-1ad8a41a-429f-4997-9d74-3156727e0402.png)

- 一定要`toml`格式

  ```pwsh
    ./meilisearch --config-file-path="D:/Meilisearch/config.toml"
  ```
  
###### 那toml內的設定長什麼樣?

- 官方有提供範例

    ```bash
       curl https://raw.githubusercontent.com/meilisearch/meilisearch/latest/config.toml > config.toml
    ```

- 我的設定, 我只有用這些

  ```toml

    db_path = "D:/Meilisearch/data.ms"
    env = "development"
    http_addr = "127.0.0.1:7700"
    master_key = "YOUR_MASTER_KEY_VALUE"
    no_analytics = true
    http_payload_size_limit = "150 MB"
    log_level = "DEBUG"
    max_indexing_memory = "4 GiB"
    max_indexing_threads = 2

    #############
    ### DUMPS ###
    #############

    dump_dir = "D:/Meilisearch/dumps/"
    # import_dump = "D:/Meilisearch/dumps/file.dump"
    ignore_missing_dump = false
    ignore_dump_if_db_exists = false

    #################
    ### SNAPSHOTS ###
    #################

    schedule_snapshot = 3600
    snapshot_dir = "D:/Meilisearch/snapshots/"
    # import_snapshot = "D:/Meilisearch/snapshots/"
    ignore_missing_snapshot = false
    ignore_snapshot_if_db_exists = false

  ```

#### 如果是使用系統環境變數呢?

```pwsh
    # 先設定變數
    set MEILI_DB_PATH=./meilifiles
    set MEILI_HTTP_ADDR=127.0.0.1:7700
    # 再啟動MeiliSearch
    ./meilisearch
```

### 參考資料

- [MeiliSearch - Configure Meilisearch at launch](https://docs.meilisearch.com/learn/configuration/instance_options.html#command-line-options-and-flags)

