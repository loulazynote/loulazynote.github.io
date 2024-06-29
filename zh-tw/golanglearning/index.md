# Golang Learning Point


# Golang Learning Point

### 開發 GO 的原因

1. 簡潔
2. 編譯快
3. 編譯式語言
4. 強型別
5. 內建併行功能(Concurrent)
6. 自動記憶體回收

### 適用 GO 的應用情景

1. Web Development
2. Command-line Interfaces(CLIs)
3. Cloud & NetWork Services

### 相容性(Compatibility of GO)

> GO 1.X 新版本保證會向下相容, 除了以下四點

1. 安全性錯誤
2. 規格錯誤
3. 非預期的行為
4. Bugs

### 常見 GO CLI 命令 執行程式

1. `go run .` (執行程式)
2. `go build .`(建置專案)
   > `go build -v -o dist/main .` (建置時顯示詳細記錄並指定輸出路徑)
3. `go clean` (清空專案-刪除建置過程產生的檔案)
   - `go clean -i` (連同已安裝的程式一併刪除)
   - `go clean -i -n` (加上 `-n` 代表預覽會刪除的檔案)
   - `go clean -cache` (清空建置快取檔案)
   - `go clean -cache -n` (預覽清空建置快取檔案)
   - `go clean -modcache -x` (清空模組快取同時顯示刪除命令)
   - `go clean -i -cache -modcache -x`

### 常見 GO CLI 命令 執行測試

1. 檔名規則： \*\_test.go
2. 執行程式： 無法使用 ( 使用 go run . 會自動忽略 \*\_test.go 檔案 )
3. 建置專案： 不用建置 ( 使用 go build 會自動忽略 \*\_test.go 檔案 )
   - 你可以用 `go test -c` 編譯測試程式（但不會執行測試）
   - 執行測試： `go test .`
   - 清空快取： `go clean -testcache`
   - 效能測試： `go test -bench=. -v`

### 常見 GO CLI 命令

1. `go version` (顯示 GO 執行環境版本)
2. `go env` (顯示 GO 相關環境變數)
3. 其他被開發工具完美整合的命令
   - `go get` 下載套件
   - `go fmt` 格式化程式碼
   - `go vet` 檢查原始碼品質
   - `go fix` 自動修復有問題的原始碼
   - `go doc` 從原始碼中取得 API 說明文件

## Go 具有跨平台的特性

### 保留字(KeyWord)

![25Keyword](https://i.imgur.com/MtmW1vo.jpg)

[Keyword 參考資料](https://golang.org/ref/spec#Keywords)

### 自動加分號規則

1. 識別字(identifier)
2. 值(value)
   - `integer`, `floating-point`, `imaginary`, `rune`, `string`
   - `true` 及 `false` 並不是值
3. 以下四個關鍵字
   - `break`
   - `continue`
   - `return`
   - `fallthrough`
4. 以下五個運算子與標點符號
   - `)`
   - `}`
   - `]`
   - `++`
   - `--`

### 命名常規 (Naming Convertion)

![Naming Convertion](https://i.imgur.com/mICpi7Q.jpg)

### 宣告變數 (Declaration)

> `var score int = 95`

- `var` 是 keyword
- `score` 是變數名稱
- `int` 是型別
- `95` 是初始值

**"`:=`" 是 宣告變數並賦值**
**有 "`*`"在前的是指標**

變數宣告有兩種方式

1.

```go
 var i = "G is" + " for Go "
 var j = 'v'
 var k1, k2 = true, !k1
 const l = 111*10000 + 9
 const m1 = math.pi / 3.141592
```

2.

```go
var (
    name string = "Earth"
    desc string = "Planet"
    radius int32 = 6378
    mass float64 = 5.972E+24
    active bool = true
    satellites []string
)
```

### 零值 (zero value)

![零值表](https://i.imgur.com/ZMLgTP3.jpg)

### 自動型別推導 (Type Inference)

`var name = "will"`
簡略寫法
`name := "will"`

### 宣告常數 (僅可使用基礎型別)

- 數字： `byte`, `int`, `int8`, `int16`, `int32`, `int64`, `rune`, `uint`, `uint8`, `uint16`, `uint32`, `uint64`, `uintptr`, `float32`, `float64`, `complex64`, `complex128`

- 布林： `bool`
- 字串： `string`

### 未定義型別的常數 (untyped constant)

![untyped constant](https://i.imgur.com/Xvsx2Ho.jpg)

### iota 遞增常數

```go
const (
    Zero = iota
    One
    Two
    Three
    Four
)
```

相等於

```go
const (
    Zero = 0
    One = 1
    Two = 2
    Three = 3
    Four = 4
)
```

## 參考資料

- [Golang 官方網站](https://golang.org/)
  - [Golang 官方文件](https://golang.org/doc/)
  - [Golang 官方 Blog](https://blog.golang.org/)

