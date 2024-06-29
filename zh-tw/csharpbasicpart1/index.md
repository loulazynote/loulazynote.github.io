# C# 基礎複習Note(型別系統)



## 型別系統 概觀

:exclamation: 是強型別語言, 每個變數和常數都有型別 :exclamation:
> 在 C# bool 中無法轉換成 int

- 儲存在類型中的資訊可以包含下列
  - 型別的變數需要的儲存空間
  - 它可以代表的最大值和最小值
  - 它所包含的成員 (方法、欄位、事件等等)
  - 它繼承自的基底型別
  - `interface` (實作)
  - 允許的作業類型

> 1. 編譯器會將型別資訊視為中繼資料內嵌至可執行檔
> 1. 通用語言執行平台 (_CLR_) 會在執行階段使用該中繼資料，以在它配置和回收記憶體時，進一步保證型別安全

### 變數宣告中指定類型

:exclamation: 程式中宣告變數或常數時 必須指定其類型 :exclamation:
> 也能使用`var`關鍵字來讓編譯器推斷類型

```cs
// Declaration only
string name;
int studentId;
AClass aclass;

// Declaration with initializers (four examples)
string studentName = "Lou";
string [] ClassList = {"Elaine","Louis","Kyber", "Peter","Jeff","Mary"}
// 型別推斷
var query = ClassList.Where(q => q == studentName).First();

```

### 內建類型

- C# 提供一組標準[內建類型](https://tinyurl.com/2j6xjgu4)
  - 整數
  - 浮點值
  - 布林運算式
  - 文字字元
  - 十進位值
  - string
  - object
  
### 自訂類型

- 可以使用 結構類型(_[struct](https://tinyurl.com/2klwttj8)_)、 類別(_[Class](https://tinyurl.com/2ns73j68)_) 、 `interface`(_[interface](https://tinyurl.com/2zknngl5)_)、列舉 (_[enum](https://tinyurl.com/2l6vose2)_) 和 記錄(_[record](https://tinyurl.com/2m3u9yuv)_) 建構來建立您自己的自訂類型
- 當明確將專案參考新增至定義這些專案的元件時，其他專案才可用
    > 編譯器在有該組件的參考之後，您可以針對在原始程式碼的那個組件中宣告的型別宣告變數 (或常數)
- [.NET 類別庫](https://tinyurl.com/2llr8ev4)本身是自訂類型的集合，可以在應用程式中使用
    > 根據預設，類別庫中最常使用的型別可用於任何 C# 程式

### 一般型別系統(_CTS_)

- 支援繼承原則
  - 型別可以衍生自稱為「基底型別」的其他型別, 而衍生的型別會繼承 (有部份限制) 基底型別的方法、屬性和其他成員
  - 基底型別同樣可以衍生自一些其他型別，所衍生的型別會繼承其繼承階層架構中兩個基底型別的成員
  - 所有類型 最終衍生自單一基底類型，即 [System.Object](https://tinyurl.com/2jzlkbkh) (C# 關鍵字：[object](https://tinyurl.com/2jzlkbkh))
  - 這種統一型別階層架構稱為[一般型別系統](https://tinyurl.com/2olfyvgz) (_CTS_)
- [一般型別系統](https://tinyurl.com/2olfyvgz) (_CTS_)中的每個型別都會定義為「實值型別」或「參考型別」
  - 包括 .NET 類別庫中的所有自訂類型
  - 使用者定義型別
  - 使用 結構類型(_struct_) 關鍵字定義的類型為實值型別，所有內建的數數值型別都是 結構類型(_struct_)
  - 使用 類別(_class_) 或 記錄(_record_) 關鍵字定義的類型是參考型別
  - 參考型別和實值型別有不同的編譯時期規則和不同的執行階段行為

  ![CTS 中的實值型別和參考型別之間的關聯性](https://docs.microsoft.com/zh-tw/dotnet/csharp/programming-guide/types/media/index/value-reference-types-common-type-system.png)

> - C# 9.0 新增 記錄型別
> - 資料和行為是類別、結構或記錄 的成員
> - 類別、結構或記錄宣告就像是用來在執行時間建立實例或物件的藍圖

```cs
//p 為 Person 的物件或執行個體
Person p = new Person(){
  Name = "Lou",
  Age = 18
};
// 可以建立多個相同 Person 型別的執行個體，且每個執行個體在其屬性與欄位中都可以有不同的值
Person p2 = new Person(){
  Name = "Elaine",
  Age = 18
};

// Person 的類別、結構
public class Person{
  public string Name { get; set; }
  public int Age { get; set; }
}

```

#### 類別是參考型別

1. 建立型別的物件時，指派物件的變數只會保留該記憶體的參考
1. 當物件參考指派至新的變數時，新的變數會參考到原始物件
1. 透過某個變數所做的變更會反映在其他變數中，因為它們都參考相同的資料

#### 結構是實值型別

1. 建立結構時，結構指派的變數會保留結構的實際資料
2. 當結構指派給新的變數時，就會複製它
3. 新的變數和原始變數會各自包含一份相同的資料，對一個複本所做的變更不會影響另一個複本

#### 記錄類型可以是參考型別(`record class`)或實值型別(`record struct`)

> - 類別 是用來建立更複雜的行為模型
>   - 類別通常會儲存在建立類別物件之後要修改的資料
> - 結構 最適合小型資料結構
>   - 結構通常會儲存在建立結構之後不打算修改的資料
> - 記錄類型 是具有其他編譯器合成成員的資料結構
>   - 記錄通常會儲存在建立物件之後不打算修改的資料

### 值類型

> 實值型別衍生自 [System.ValueType](https://tinyurl.com/2kurpe55)，該型別又衍生自 [System.Object](https://tinyurl.com/2jzlkbkh)

- 實數值型別變數會直接包含其值
  - 結構記憶體會內嵌配置於變數所宣告的任何內容中，實數值型別變數沒有個別的堆積配置或垃圾收集額外負荷

- 實值型別有兩種類別
  > 實值型別為 **密封**, 無法從任何實值型別衍生型別
  - 結構類型(_[struct](https://tinyurl.com/2klwttj8)_)
  - 列舉 (_[enum](https://tinyurl.com/2l6vose2)_)

```cs
// 內建的數數值型別是結構，而且其具有您可以存取的欄位和方法
// constant field on type byte.
byte b = byte.MaxValue;

// 宣告並指派值給它們，就像是簡單的非匯總類型
byte num = 0xA;
int i = 5;
char c = 'Z';
```

使用 結構類型 來建立自訂實值型別:

```cs
public struct Coords
{
    public int x, y;

    public Coords(int p1, int p2)
    {
        x = p1;
        y = p2;
    }
}
```

實值型別的另一個類別是 `enum`:

```cs
// 列舉會定義一組具名的整數常數
public enum FileMode
{
    CreateNew = 1,
    Create = 2,
    Open = 3,
    OpenOrCreate = 4,
    Truncate = 5,
    Append = 6,
}
```

### [參考型別](https://tinyurl.com/2j9vb5wj)

其定義為  類別(_class_), 記錄(_record_), 委派(_delegate_), 陣列(_array_)或 `interface`(_interface_)的類型是參考型別

- 參考型別完全支援繼承
  - 當您建立類別時，可以繼承自未定義為 密封的任何其他`interface`或類別
  - 其他類別可以繼承自您的類別，並覆寫您的虛擬方法

類別的建立和指派 Example:

```cs
// 建立
MyClass myClass = new MyClass();
// 指派
MyClass myClass2 = myClass;
```

`interface`(interface) 無法使用`new` 運算子直接具現化，請建立並指派實作 `interface`之類別的實例 Example:

```cs
// 建立
MyClass myClass = new MyClass();

// 用現值宣告及賦值
IMyInterface myInterface = myClass;

// 或 建立並賦值 給`interface`
IMyInterface myInterface2 = new MyClass();
```

- 所有陣列都是參考型別，即使其元素都是實值型別
  - 陣列會隱含衍生自[System.Array](https://tinyurl.com/2mvstzr3) 類別，可以宣告並使用 搭配 C# 提供的簡化語法

  ```cs
  // 宣告並初始化一個數字陣列
  int[] nums = { 1, 2, 3, 4, 5 };

  // 訪問 System.Array 的實例屬性
  int len = nums.Length;
  ```

### 泛型類型

類型可以使用一或多個 類型參數 來宣告，做為實際型別的預留位置

建立 類型的實例時，可以指定清單將包含的物件類型，例如 `string`：

```cs
// <>即為泛型,此處為 包含string型別的清單
List<string> stringList = new List<string>();
stringList.Add("String example");
// compile time error adding a type other than a string:
stringList.Add(4);
```

> - 使用型別參數(\<T>)讓您能夠重複使用相同的類別來保存任何元素型別，而不需要將每個元素都轉換成 `object`
> - 泛型集合類別稱為 **強型別集合** ，因為編譯器知道集合元素的特定類型

### 隱含型別、匿名型別和可為 Null 的實值型別

- 隱含型別 : 可以使用 `var` 隱含輸入區域變數（但不能輸入類別成員），其變數還是會在編譯時期收到型別，但其是由編譯器所提供的型別
- 匿名型別 : 針對不想要在外部方法 儲存或傳遞的簡單相關值集合，建立具名類型可能很不方便，為此，可以建立「匿名型別」
- 可為 Null 的實值型別 : 一般實值型別不能有 [`null`](https://tinyurl.com/2zpsr3m3) 的值, 在類別後附加`?`後，允許建立可為 Null 的實值型別，例如， `int?` 是一種 `int` 類型，也可以有 值 [`null`](https://tinyurl.com/2zpsr3m3)
  - 可為 Null 的實值型別是泛型結構類型的 [`System.Nullable<T>`](https://tinyurl.com/2hpary95) 實例。 當您將資料傳入資料庫時，可為 Null 的實值型別特別有用，其中數值可能是 [`null`](https://tinyurl.com/2zpsr3m3)

### 編譯時間類型和執行時間類型

- 變數可以有不同的編譯時間和執行時間類型
  1. 編譯時間類型是原始程式碼中變數的宣告或推斷類型
  2. 執行時間類型是該變數所參考之實例的類型
  
  - 這兩種類型通常相同,Example:

    ```cs
    string message = "This is a string of characters";
    ```

  - 在其他情況下，編譯時間類型不同,Example:

    ```cs
    // 編譯時間類型位於 object, 執行時間類型為 string
    object anotherMessage = "This is another string of characters";

    // 編譯時間類型位於 IEnumerable<char>, 執行時間類型為 string
    IEnumerable<char> someCharacters ="abcdefghijklmnopqrstuvwxyz";
    ```

> 變數的兩種類型不同，請務必瞭解編譯時間類型和執行時間類型套用的時間，而編譯時間類型會決定編譯器所採取的所有動作

## 命名空間(宣告命名空間以組織類型)

- C# 程式設計大量使用命名空間的原因有兩個
    1. .NET 會使用命名空間來組織其許多類別

        ```cs
        // System 是命名空間，而 Console 是該命名空間中的類別
        System.Console.WriteLine("Hello World!");
        ```

        ```cs
        // using關鍵字可用來讓完整名稱不需要
        using System;


        Console.WriteLine("Hello World!");
        ```

    1. 宣告您自己的命名空間，將有助於在較大型的程式設計專案中控制類別和方法名稱的範圍

       ```cs
       // 使用 namespace 關鍵字宣告命名空間
       namespace SampleNamespace
        {
            class SampleClass
            {
                public void SampleMethod()
                {
                    System.Console.WriteLine(
                        "SampleMethod inside SampleNamespace");
                }
            }
        }
       ```

       > 命名空間的名稱必須是有效的 C# [識別碼名稱](https://tinyurl.com/2logktpn)

       ```cs
       // 從 C# 10 開始，您可以針對該檔案中定義的所有類型宣告命名空間
        namespace SampleNamespace;

        class AnotherSampleClass
        {
            public void AnotherSampleMethod()
            {
                System.Console.WriteLine(
                    "SampleMethod inside SampleNamespace");
            }
        }
       ```

### 命名空間概觀

- 命名空間具有下列屬性：
  - 命名空間可組織大型程式碼專案
  - 它們會使用`.`運算子來分隔
  - `using` 指示詞讓其不需要指定每個類別的命名空間名稱
  - `global` 命名空間是 "root" 命名空間：`global::System` 一律會參考  [.NET System](https://tinyurl.com/2q95y9ln) 命名空間

## 類別

- 參考型別 : 定義為類別(_Class_)的類型是參考型別
  - 執行時間，當宣告參考型別的變數時，該變數會包含值 null ，直到使用 new 運算子明確建立類別的實例，或指派可能已在其他地方建立之相容型別的物件

    ```cs
    //Declaring an object of type MyClass.
    MyClass mc = new MyClass();

    //Declaring another object of the same type, assigning it the value of the first object.
    MyClass mc2 = mc;
    ```

- 宣告類別 : 類別是使用 class 關鍵字來宣告，後面接著唯一識別碼

    ```cs
    // class 關鍵字的前面會加上存取層級(public)
    // [access modifier] - [class] - [identifier]
    public class Customer
    {
      // 類別上的欄位、屬性、方法和事件統稱為「類別成員」
      // Fields, properties, methods and events go here...
    }
    ```

- 建立物件 : 物件是根據類別的具體實體，而且有時稱為類別的執行個體
  > 類別會定義一種類型的物件，但不是物件本身

  ```cs
      // 使用 new 關鍵字來建立物件
      // object1 是根據 Customer 之物件的參考
      Customer object1 = new Customer();
      // 可以建立物件參考，而根本不需要建立物件
      Customer object2;
  ```

  > 建立物件參考，如未參考上一個物件參考，嘗試透過這類參考來存取物件將會在執行時間失敗

  ```cs
     //可以藉由建立新的物件，或為其指派現有的物件，來參考物件
     Customer object3 = new Customer();
     Customer object4 = object3;
  ```

- 類別繼承 : 類別完全支援「繼承」，這是物件導向程式設計的基礎特性
  - 建立類別時，可以繼承自任何其他未定義為 [sealed](https://tinyurl.com/2zaxogyp) 的類別，而其他類別可以繼承自您的類別，並覆寫類別虛擬方法，且可以執行一或多個`interface`
  - 使用「衍生」可完成繼承，這表示使用從中繼承資料和行為的「基底類別」來宣告類別。 附加冒號以及接著衍生類別名稱後面的基底類別名稱，以指定基底類別
  - 類別宣告基底類別時，會繼承基底類別的所有成員，但建構函式除外

     ```cs
      public class Manager : Employee
      {
          // Employee fields, properties, methods and events are inherited
          // New Manager fields, properties, methods and events go here...
      }
     ```

  - 可用[`abstract`](https://tinyurl.com/2qa5renl) 宣告類別
    - 抽象類別包含具有簽章定義但沒有實作的抽象方法， 無法具現化抽象類別
    - 它們僅用於實作抽象方法的衍生類別
    - 與[`sealed`](https://tinyurl.com/2zaxogyp)類別相反，`sealed`不允許從它衍生其他類別

## 記錄

C# 中的 記錄 是一種 **類別** 或 **結構** ，可提供使用資料模型的特殊語法和行為

### 使用記錄的時機

- 您想要定義相依于"[**值相等**](https://bit.ly/3t7a5zp)"的資料模型
- 您想要定義物件為"**不可變**"的類型

#### 實值相等

- 對於記錄而言，值相等表示如果類型相符且所有屬性和域值相符，則記錄類型的兩個變數會相等
  - 對於其他參考型別（例如類別），相等表示 參考相等

> 並非所有資料模型都能搭配值相等來運作， 例如 : `Entity Framework Core` 取決於參考是否相等，以確保它只針對概念為一個實體的實體類型使用一個實例
> ， 基於這個理由，記錄類型不適合用來做為 `Entity Framework Core` 中的實體類型

#### 不變性

- 不可變的型別是一種可防止在物件具現化之後，變更該物件的任何屬性或域值
  - 需要型別必須是安全線程，或者您是根據雜湊表中剩餘的雜湊碼而定時，永久性可能很有用(`JWTToken`、`EnycrptPassword`)
  - 記錄提供簡潔的語法來建立和使用不可變的類型

> 永久性並不適用于所有資料案例， 例如 : `Entity Framework Core`不支援使用不可變的實體類型進行更新

### 記錄與類別和結構有何不同

- 宣告和[具現化](https://bit.ly/3M47VYc)類別或結構的相同語法可用於記錄
  - 只需以關鍵字取代 `class` ，或使用 `record struct` 取代 `struct`
- `record` 同樣地，記錄類別也支援用來表示繼承關聯性的相同語法
  - 記錄與類別的差異如下：
    - 您可以使用 [位置參數](https://bit.ly/38WXJTX) ，利用不可變的屬性來建立和具現化型別
    - 在**類別**中指出參考相等或不相等的相同方法和運算子 (例如 [Object.Equals(Object)](https://bit.ly/3tcTUkp) 和 `==`) ，表示**記錄**中的 [Object.Equals(Object)](https://bit.ly/3tcTUkp) 不相等
        > 可以用 `Object.Equals(Object)`檢測是否相等
    - 您可以使用 運算式來建立不可變物件的複本，並在選取的屬性中包含新的值
    - 記錄的 `ToString`方法會建立格式化的字串，以顯示物件的類型名稱以及其所有公用屬性的名稱和值
    - 記錄可以 [繼承自另一個記錄](https://bit.ly/3GKv2WQ)， <span style="color:red;">記錄無法繼承自類別，而且類別無法繼承自記錄</span>
  - 記錄結構與結構的不同之處在於
    1. 編譯器合成了相等的方法和 ToString
    2. 編譯器為位置記錄結構合成 Deconstruct 方法

#### Example

```cs
// 定義公開記錄
public record Person(string FirstName, string LastName);

public static void Main()
{
    // 使用位置參數來宣告和具現化記錄
    Person person = new("Nancy", "Davolio");
    // 列印類型名稱和屬性值
    Console.WriteLine(person);
    // output: Person { FirstName = Nancy, LastName = Davolio }
}
```

```cs
// 定義公開記錄
public record Person(string FirstName, string LastName, string[] PhoneNumbers);

public static void Main()
{
    var phoneNumbers = new string[2];
    // 使用位置參數來宣告和具現化記錄
    Person person1 = new("Nancy", "Davolio", phoneNumbers);
    Person person2 = new("Nancy", "Davolio", phoneNumbers);
    Console.WriteLine(person1 == person2); // output: True

    person1.PhoneNumbers[0] = "555-1234";
    // 位置及長度相同
    Console.WriteLine(person1 == person2); 
    // output: True

    // 不同的執行個體也不為 null
    Console.WriteLine(ReferenceEquals(person1, person2)); 
    // output: False
}
```

```cs
// 定義公開記錄
public record Person(string FirstName, string LastName)
{
    public string[] PhoneNumbers { get; init; }
}

public static void Main()
{
    Person person1 = new("Nancy", "Davolio") { PhoneNumbers = new string[1] };
    Console.WriteLine(person1);
    // output: Person { FirstName = Nancy, LastName = Davolio, PhoneNumbers = System.String[] }

    // 使用 with 運算式來複製不可變的物件，並變更其中一個屬性
    Person person2 = person1 with { FirstName = "John" };
    Console.WriteLine(person2);
    // output: Person { FirstName = John, LastName = Davolio, PhoneNumbers = System.String[] }
    Console.WriteLine(person1 == person2); // output: False

    person2 = person1 with { PhoneNumbers = new string[1] };
    Console.WriteLine(person2);
    // output: Person { FirstName = Nancy, LastName = Davolio, PhoneNumbers = System.String[] }
    Console.WriteLine(person1 == person2); 
    // output: False
    // 因 new string[1]是一個新的object與原object不相同

    person2 = person1 with { };
    Console.WriteLine(person1 == person2); 
    // output: True
    // 因複製了卻無改變其值
}
```

## 介面(定義多個類型的行為)

1. `interface`包含非抽象 類別(`class`) 或 結構(`struct`) 必須實作之相關功能群組的定義
2. `interface`可以定義 靜態(`static`) 必須具有實作的方法

    > 從 C# 8.0 開始，`interface`可能會定義成員的預設實作

    - 可以藉由使用`interface`，在類別中包含多個來源的行為(這項功能在 C# 中是很重要的，因為語言不支援類別的多重繼承)
    - 如果要模擬結構繼承，則必須使用`interface`，因為它們實際上無法繼承自另一個結構或類別
    - 使用 [`interface`](https://bit.ly/3targQX) 關鍵字來定義`interface`:

        ```cs
        interface IEquatable<T>
        {
            bool Equals(T obj);
        }
        // 任何實作 IEquatable<T> `interface`的類別或結構，必須包含 Equals 方法的定義，該方法符合`interface`指定的簽章
        ```

  > - `interface`的名稱必須是有效的 C# 識別碼名稱(依慣例，`interface`名稱以大寫字母 I 開頭)
  > - [IEquatable\<T>](https://bit.ly/3m4YPzF)的定義沒有提供 `Equals`的實作
  > - 類別 或 結構 可以實現多個`interface`，但 類別 只能繼承自 單一類別

3. `interface`可以包含實作方法、屬性、事件、索引子，或這四個成員類型的任何組合
4. `interface`可能包含靜態建構函式、欄位、常數或運算子

    > C# 11 開始，不是欄位的`interface`成員可能是 `static abstract`

5. `interface`不能包含實例欄位、實例建構函式或完成項(`interface`是無法被實例化的)
6. `interface`成員預設為公用，而且可以明確指定協助工具修飾詞，EX: `public` 、 `protected` 、 `internal` 、 `private` 、 `protected internal` Or `private protected`
7. 成員 `private` 必須具有預設實作

> - 若要實作`interface`成員，實作類別的對應成員必須是公用、非靜態，且具有與`interface`成員相同的名稱和簽章
> - :exclamation: 當`interface`宣告靜態成員時，實作該`interface`的類型也可能宣告具有**相同簽章**的靜態成員， 這些是宣告成員的型別有所區別且是唯一識別的， 在型別中宣告的靜態成員 **不會覆寫** `interface`中所宣告的靜態成員 :exclamation:

- `class`/`struct`繼承了`interface`必須實作該`interface`的所有成員，而不需要`interface`提供預設實作
  - 如果基底類別實作`interface`，則衍生自基底類別的任何`class`/`struct`都會繼承該實作
  - `interface`也能繼承`interface`(一或多個)
  - `class`/`struct`繼承了`interface`,而此`interface`如有繼承其他`interface`,則該`class`/`struct`必須實作出所有繼承鏈中**所有`interface`的成員**
    - 該`class`/`struct`可能會隱含轉換成衍生`interface`或其任何基底`interface`
    - `class`/`struct`可能透過基底類別包含`interface`多次，繼承或透過其他`interface`繼承的`interface`
  - 只有在類別將`interface`宣告為類別 (`class ClassName : InterfaceName`) 定義的一部分時，類別只能提供`interface`實作一次

#### Example

```cs
// 類別的屬性與索引子可以針對`interface`中定義的屬性或索引子定義額外的存取子
public class Car : IEquatable<Car>
{
    // 實作`interface`的類別可以宣告具有 get 和 set 存取子的相同屬性
    public string? Make { get; set; }
    public string? Model { get; set; }
    public string? Year { get; set; }

    // 屬性或索引子使用明確的實作，則存取子必須相符
    // Implementation of IEquatable<T> interface
    public bool Equals(Car? car)
    {
        return (this.Make, this.Model, this.Year) ==
            (car?.Make, car?.Model, car?.Year);
    }
}
```

##### 如果類別實作兩個具有相同簽章成員的介面，則在類別上實作該成員會造成這兩個介面都使用該成員進行實作

```cs

public interface IControl
{
    void Paint();
}
public interface ISurface
{
    void Paint();
}
public class SampleClass : IControl, ISurface
{
    // Both ISurface.Paint and IControl.Paint call this method.
    public void Paint()
    {
        Console.WriteLine("Paint method in SampleClass");
    }
}
```

```cs
SampleClass sample = new SampleClass();
IControl control = sample;
ISurface surface = sample;

// The following lines all call the same method.
sample.Paint();
control.Paint();
surface.Paint();

// Output:
// Paint method in SampleClass
// Paint method in SampleClass
// Paint method in SampleClass
```

- 若要根據使用中的介面來呼叫不同的執行，可以明確地執行介面成員，明確的介面實作為僅透過指定介面呼叫的類別成員:

```cs
public class SampleClass : IControl, ISurface
{
    void IControl.Paint()
    {
        System.Console.WriteLine("IControl.Paint");
    }
    void ISurface.Paint()
    {
        System.Console.WriteLine("ISurface.Paint");
    }
}
```

- 類別成員 `IControl.Paint` 只能透過 `IControl` 介面取得，`ISurface.Paint` 只能透過 `ISurface` 取得(這兩種方法都是分開的，而且不會直接在類別上使用)

```cs
SampleClass sample = new SampleClass();
IControl control = sample;
ISurface surface = sample;

// The following lines all call the same method.
//sample.Paint(); // Compiler error.
control.Paint();  // Calls IControl.Paint on SampleClass.
surface.Paint();  // Calls ISurface.Paint on SampleClass.

// Output:
// IControl.Paint
// ISurface.Paint
```

##### 若要同時執行這兩個介面，類別必須使用明確的實作為屬性 P 或方法 P （或兩者），以避免編譯器錯誤

```cs
interface ILeft
{
    int P { get;}
}
interface IRight
{
    int P();
}

class Middle : ILeft, IRight
{
    public int P() { return 0; }
    int ILeft.P { get { return 0; } }
}
```

##### 從 c # 8.0開始，可以為介面中所宣告的成員定義實作為

- 如果類別從介面繼承方法執行，則只能透過介面類別型的參考來存取該方法，繼承的成員不會顯示為公用介面的一部分

```cs
public interface IControl
{
    void Paint() => Console.WriteLine("Default Paint method");
}
public class SampleClass : IControl
{
    // Paint() is inherited from IControl.
}
```

```cs
var sample = new SampleClass();
//sample.Paint();// "Paint" isn't accessible.
var control = sample as IControl;
control.Paint();
// 任何實介面的類別 IControl 都可以覆寫預設 Paint 方法，例如公用方法，或做為明確的介面執行
```

## 泛型

泛型會將型別參數的概念引進 .NET，讓您能夠設計類別和方法來延遲一或多個型別的規格，直到用戶端程式代碼宣告並具現化類別或方法為止

### 藉由使用泛型型別參數 `T` ，您可以撰寫可供其他用戶端程式代碼使用的**單一類別**，而不會產生執行時間轉換或裝箱作業的成本或風險

  ```cs
  // Declare the generic class.
  public class GenericList<T>
  {
      public void Add(T input) { }
  }
  class TestGenericList
  {
      private class ExampleClass { }
      static void Main()
      {
          // Declare a list of type int.
          GenericList<int> list1 = new GenericList<int>();
          list1.Add(1);

          // Declare a list of type string.
          GenericList<string> list2 = new GenericList<string>();
          list2.Add("");

          // Declare a list of type ExampleClass.
          GenericList<ExampleClass> list3 = new GenericList<ExampleClass>();
          list3.Add(new ExampleClass());
      }
  }
  ```

- 泛型類別和方法結合了重複使用性、型別安全和效率
- 泛型最常搭配在其上操作的集合和方法使用
- [System.Collections.Generic](https://bit.ly/3x901bC)命名空間包含數個以泛型為基礎的集合類別
- 非泛型集合（例如 [ArrayList](https://bit.ly/3aCea8C) ）不建議使用

### 建立自訂的泛型型別和方法，簡單的泛型類別

> 當 `GenericArray<T>` 以具象類型具現化時 (例如具現化為 `GenericArray<int>`)，所出現的每個 `T` 都會以 `int` 取代

  ```cs
  public class GenericArray<T>
  {
      private T[] array;
      public GenericArray(int size)
      {
          array = new T[size + 1];
      }
      public T getItem(int index)
      {
          return array[index];
      }
      public void setItem(int index, T value)
      {
          array[index] = value;
      }
  }
  ```

- 使用泛型 GenericArray<T> 類別並輸出結果:

    ```cs
    class Tester
    {
        static void Main(string[] args)
        {
            //declaring an int array
            MyGenericArray<int> intArray = new MyGenericArray<int>(5);
            //setting values
            for (int c = 0; c < 5; c++)
            {
                intArray.setItem(c, c*5);
            }
            //retrieving the values
            for (int c = 0; c < 5; c++)
            {
                Console.Write(intArray.getItem(c) + " ");
            }
            Console.WriteLine();
            //declaring a character array
            MyGenericArray<char> charArray = new MyGenericArray<char>(5);
            //setting values
            for (int c = 0; c < 5; c++)
            {
                charArray.setItem(c, (char)(c+97));
            }
            //retrieving the values
            for (int c = 0; c< 5; c++)
            {
                Console.Write(charArray.getItem(c) + " ");
            }
            Console.WriteLine();
            Console.ReadKey();
        }
    }
    //OutPut:
    //0 5 10 15 20
    //a b c d e
    ```

- 泛型方法 Example 2:

    ```cs

    class Program
        {
            static void Swap<T>(ref T lhs, ref T rhs)
            {
                T temp;
                temp = lhs;
                lhs = rhs;
                rhs = temp;
            }
            static void Main(string[] args)
            {
                int a, b;
                char c, d;
                a = 10;
                b = 20;
                c = 'I';
                d = 'V';

                //display values before swap:
                Console.WriteLine("Int values before calling swap:");
                Console.WriteLine("a = {0}, b = {1}", a, b);
                Console.WriteLine("Char values before calling swap:");
                Console.WriteLine("c = {0}, d = {1}", c, d);

                //call swap
                Swap<int>(ref a, ref b);
                Swap<char>(ref c, ref d);

                //display values after swap:
                Console.WriteLine("Int values after calling swap:");
                Console.WriteLine("a = {0}, b = {1}", a, b);
                Console.WriteLine("Char values after calling swap:");
                Console.WriteLine("c = {0}, d = {1}", c, d);
                Console.ReadKey();
            }
        }
        /// OutPut:
        /// Int values before calling swap:
        /// a = 10, b = 20
        /// Char values before calling swap:
        /// c = I, d = V
        /// Int values after calling swap:
        /// a = 20, b = 10
        /// Char values after calling swap:
        /// c = V, d = I
    ```

### 泛型總覽

- 使用泛型型別以最佳化程式碼重複使用、型別安全和效能
- 泛型的最常見用法是建立集合類別
- .NET 類別庫包含命名空間中 [System.Collections.Generic](https://bit.ly/3x901bC) 有數個泛型集合類別，應該盡可能使用泛型集合，而不是命名空間中 [System.Collections](https://bit.ly/3m5gmrt) 的類別 [ArrayList](https://bit.ly/3aCea8C)
- 可以建立自己的泛型介面、類別、方法、事件和委派
- 泛型類別可限制為允許存取特定資料類型上的方法
- 泛型資料類型中所使用的類型相關資訊，可在執行階段透過反映取得

## 匿名類型

- 匿名類型提供一個便利的方法，將一組唯讀屬性封裝成一個物件，而不需要事先明確定義類型
  - 類型名稱會由編譯器產生，並且無法在原始程式碼層級使用
  - 每個屬性的類型會由編譯器推斷

  ```cs
  // 以兩個名為 Amount 和 Message 的屬性初始化的匿名類型
  var v = new { Amount = 108, Message = "Hello" };

  // Rest the mouse pointer over v.Amount and v.Message in the following
  // statement to verify that their inferred types are int and string.
  Console.WriteLine(v.Amount + v.Message);
  ```

 > 匿名型別通常用於查詢運算式的`select`子句中 ，以從來源序列中的每個物件傳回屬性的子集

- 匿名類型包含一個或多個公用唯讀屬性
  - 其他類型的類別成員 (例如方法或事件) 則無效
  - 用於初始化屬性的運算式不可以是 `null`、匿名函式或指標類型

  ```cs
  var productQuery = from prod in products
                     select new { 
                                prod.Color,
                                prod.Price 
                              };

  foreach (var v in productQuery)
  {
      Console.WriteLine("Color={0}, Price={1}", v.Color, v.Price);
  }
  ```

- 可以使用 `var` 將變數宣告為隱含型別區域變數， 由於只有編譯器可以存取匿名類型的基本名稱，因此無法在變數宣告中指定類型名稱

  ```cs
    // 合併隱含類型區域變數和隱含類型陣列，以建立匿名類型項目的陣列
    var anonArray = new[] { new { name = "apple", diam = 4 }, new { name = "grape", diam = 1 }};
  ```

- 匿名型別是`class`衍生自`object`的型別，而且不能轉換成除了`object`以外的任何類型
- 如果組件中有兩個或多個匿名物件初始設定式，指定了順序相同並具有相同名稱和類型的屬性序列，編譯器會將這些物件視為相同類型的執行個體
  - 這些物件會共用編譯器產生的相同類型資訊
- 匿名型別以 運算式的形式支援非破壞性變化，這可建立匿名型別的新實例，其中一或多個屬性具有新的值

  ```cs
      var apple = new { Item = "apples", Price = 1.35 };
      var onSale = apple with { Price = 0.79 };
      Console.WriteLine(apple);
      Console.WriteLine(onSale);
  ```

- 無法將欄位、屬性、事件或方法的傳回類型，宣告為具有匿名類型
- 無法將方法、屬性、建構函式或索引子的型式參數宣告為具有匿名類型
- 若要傳遞匿名型別或包含匿名型別的集合，做為方法的引數，可以將參數宣告為類型 `object`
  - :exclamation: 針對匿名型別使用 object 會破壞強型別的目的 :exclamation:
- 如果必須在方法界限外儲存或傳遞查詢結果，請考慮使用一般具名結構或類別來取代匿名類型
- 匿名類型上的 [Equals](https://bit.ly/3Mc55R0) 和 [GetHashCode](https://bit.ly/3GPddWA) 方法會以屬性的 `Equals` 和 `GetHashCode` 方法來定義，相同匿名類型的兩個執行個體僅在其所有屬性都相等時，這兩個執行個體才相等

## 參考資料

1. [MSDN - C# 文件](https://bit.ly/3GF8SVT)
2. [Gitbook - C#教學](https://bit.ly/3NQ2Fsp)

