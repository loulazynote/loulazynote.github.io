# What is NoSQL

## 關聯式資料庫(_RDBMS_)

目前常用的資料庫,完全遵守 _[ACID](https://zh.wikipedia.org/zh-tw/ACID)_ 原則,在任何情況下(**_Atomicity_**)都可保持每個`Transaction`不成功則`roll back`,(**_Consistency_**)資料一致性,(**_Isolation_**)讀寫不互相干擾,(**_Duration_**)資料永久性(EX:`SQL Server`,`PostgreSQL`,`MySQL`...等)
且僅能支援垂直擴充,假設要應用在大量的資料的時候,只能透過硬體的升級(_CPU_ 升級、加 _HDD_、增加 _RAM_...等)

## 非關聯式資料庫(_NOSQL_)

`NOSQL`(_Non-SQL_,又為 _Not only SQL_) 稱為**非關聯式資料庫**,跟關聯式資料庫不一樣,不需要定義 `schema`且沒有關聯的關係(EX:`MongoDB`,`Redis`,`MariaDB`...)

- 非關聯式資料庫特點：
  - 資料庫由 _collection_ 組成
  - _collection_ 中每筆資料為一份 _document_,_document_ 的資料格式不需一致
  - 以 `CAP theorem` 為概念設計
  - 常用於分散式雲端系統

  ![結構示意圖](https://miro.medium.com/max/1400/1*zDFS4FVd1hBgIvmFm8FC7g.png)

## NoSQL v.s RDBMS

### RDBMS

- _RDBMS_ 因具備 _ACID_ 特性,且保證了資料的一致性,若資料需在任何情況下都穩定且一致,則推薦此類(EX: 金融系統)

### NOSQL

1. 由於 NOSQL 的 schema 不需要固定,且資料可以為任意結構,所以在 `schema` 需要頻繁變動或者是 `schema` 不固定的時候,_NOSQL_ 提供更有彈性的開發
2. 需要儲存大量資料,利用分散式系統以及雲端儲存的時候,由於 _NOSQL_ 相較於 _RDBMS_ 更容易做水平擴充 (_horizontal scaling_),且本身的設計就是分散式系統的設計,因此對於未來有大量擴充需求的系統,會更容易的去擴充,且擴充的成本也較低
3. 具備水平擴充,較不會有[Single Failed](https://zh.wikipedia.org/wiki/%E5%8D%95%E7%82%B9%E6%95%85%E9%9A%9C)

## NOSQL 類別

### 1. 資料形態(粗略分類)

- `Key-Value`:
  - `Key-Value`資料庫是 _NoSQL_ 資料庫中最大宗的類型,這類資料最大的特色就是採用`Key-Value`資料架構,取消了原本關聯式資料庫中常用的欄位架構（`Schema`）,每筆資料各自獨立,所以,可以打造出分散式和高擴充能力的特性
  - 例如: `Google BigTable`,`Hadoop HBase`,`Amazon Dynamo`,`Cassandra`,`Hypertable`,`Redis`,`Firebase`...
- [`Document`](https://zh.wikipedia.org/zh-tw/%E9%9D%A2%E5%90%91%E6%96%87%E6%AA%94%E7%9A%84%E6%95%B8%E6%93%9A%E5%BA%AB):
  - 也是一種`key-value`,限制 `value` 的結構為**半結構化資料**`semi-structure`,像是 _JSON_ 等
  - 將資訊存儲在文檔中的資料庫,旨在將資料儲存為類似 JSON 的文件並對其進行查詢
  - 文件資料庫主要是用來儲存非結構性的文件,例如最常見的非結構化資料就是`HTML`網頁,一個`HTML`網頁結構不像一般表格那樣有固定的欄位,每個欄位有特定資料類型和大小,例如網頁裡有`Head`和`Body`結構,`Body`元素中可能會有10個段落,段落中會有文字、連結、圖片等,文件資料庫的資料結構往往是鬆散的樹狀結構
  - 例如:`MongoDB`,`CouchDB`,`Lotus Notes`...
- `Wide-column`(圖學架構):
  - 運用圖學架構來儲存節點間關係資料架構(基本的圖學資料包括了節點（_Node_）、關係（_Relation_）和屬性（_Property_）三種結構)
  - 以多個`raw`、`column`進行存取,將資料拆成小資料再進行組合,之後可透過`column key` / `row key` 進行關聯查詢(可有效的壓縮資料及取用同級的資料集)
  - 例如:`Neo4j`,`InfoGrid`,`AllegroGrph`,`Sparksee`...

### 2. [CAP定理](https://zh.m.wikipedia.org/zh-hant/CAP%E5%AE%9A%E7%90%86) (Consistency, Availability, Partition Tolerance)

- _Consistency_: 一致性,讀寫都會遵循 _Atomicity_ 原則,等同於所有節點訪問同一份最新的資料副本
- _Availability_: 可用性,每次請求都能獲取到非錯的響應,但是無法保證得到的資料為最新的
- _[Partition Tolerance](https://en.wikipedia.org/wiki/Network_partition)_: 分區容錯性,電腦網路區分為相對獨立的子網,這些子網可以通過設計單獨優化,也可以是網絡設備的故障而導致,而分散式軟體必須設計成具有分區容錯性,也就是說,即使網路被分區後,仍然可以正常工作

根據定理,分散式系統只能滿足三項中的兩項而不可能滿足全部三項,因為在Available的情況下,Consistency與Partition Tolerance是互相矛盾的,因此大致的分類有 `AP`、`CA`、`CP`

- `AP`: 只要節點可用就能持續運作
- `CA`: 在沒有任何的例外下,都是可用並保證資料的一致性
- `CP`: 在節點不穩定的時候不接受 `request`

## 參考資料

- [NoSQL | wikipedia.org](https://zh.wikipedia.org/zh-tw/NoSQL)
- [什麼是 NoSQL？|  AWS](https://aws.amazon.com/tw/nosql/)
- [快速認識4類主流NoSQL資料庫 | iThome](https://www.ithome.com.tw/news/92507)
- [[淺談]-NoSQL資料庫怎麼選？ | ZiXiang-Blog](https://tinyurl.com/y6lxc4zy)
- [RDBMS vs. NOSQL - 關聯式資料庫 vs. 非關聯式資料庫 | Hs-medium](https://tinyurl.com/y3nj5tfw)
- [結構、半結構、非結構式資料是啥意思？ | Kevin的MongoDB工作筆記](https://tinyurl.com/y33fjm8o)

