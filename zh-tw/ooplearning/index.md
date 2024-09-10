# OOP 物件導向學習筆記

## 物件導向四大原則

### 封裝(_Encapsulation_)

封裝（_Encapsulation_）在物件導向程式設計中是用來將資料與函式的實作內容包裝、隱藏起來的技巧，其中資料更常被稱為類別的成員屬性（_Attributes_）、函式更常被稱為類別的成員方法（_Methods_）。  

- 「封裝」有時被稱為物件導向程式設計的第一大支柱或原則。
- 類別或結構可以指定其每個成員在類別或結構外部程式碼的可存取程度。
- 無法從類別或元件外部使用的方法和變數可以隱藏，以限制編碼錯誤或惡意探索的可能性。

> 舉個例子，如同部落格發文，公開貼文大家都看的到，設定私人或草稿除了自己其他人都看不到。

說白了，就是公開出來的才看的到（`public`）。而未公開的，就怎麼也找不到（`private`、`protected`）。

#### `public`

顧名思義, 任何人都可以存取

```cs
// 部落格基本功能都是必要的公開行為，文章抬頭、文章內容、發文
public class blog
{
    public string Title { get; set; }
    public string Content { get; set; }
    public void Post { get; set; }
}
```

```cs
public class LouNote : blog
{
    // 因繼承blog關係會有下列欄位（僅示意，在C#內繼承後不會實際顯示這些欄位，但可以使用）
    public string Title { get; set; }
    public string Content { get; set; }
    public void Post { get; set; }
    // 示意結束

    // 為文章增加分類也是公開行為
    public string[] Categories { get; set; }
}
```

#### `private`

只有 `class` 本身可以存取

```cs
public class LouNote : blog
{
    // 因繼承blog關係會有下列欄位（僅示意，在C#內繼承後不會實際顯示這些欄位，但可以使用）
    public string Title { get; set; }
    public string Content { get; set; }
    public void Post { get; set; }
    // 示意結束

    // 為文章增加分類也是公開行為
    public string[] Categories { get; set; }

    // 因存入草稿是不公開的，僅有自己看的到
    private object Draft { get; set; }
}
```

#### `protected`

自身Class及被繼承的 `class` 可以存取

```cs
public class 會員優惠
{
    // 給予5折優惠
    protected int 折扣 = 50;
    protected string 禮品 = "Iphone 14 Pro";
}

public class 消費者: 會員優惠
{
    static void Main(string[] args)
    {
        var 非本人 = new 會員優惠();// Error CS1540,因為 會員優惠 只能會員 本人使用
        
        var 本人 = new 消費者();


        // 因是會員本人,所以可以使用此折扣,且還能更改折扣為3折
        本人.折扣 = 70;
        本人.禮品 = "Iphone 14 Pro Max";
        Console.WriteLine($"此會員因消費滿額,故給予優惠 折扣 {本人.折扣}% 及贈品 {本人.禮品}!");
    }
}
// Output: 此會員因消費滿額,故給予優惠 折扣 70% 及贈品 Iphone 14 Pro Max!
```

#### C# 額外其他3種存取方式

- `internal` :
  - 類別: 相同命名空間 (相同dll存取) 可存取
  - 成員: 只有自身`Class`可存取
- `protected internal` :
  - 類別: 自身`Class`, 被繼承`Class` 皆可存取
  - 成員: 自身`Class`, 相同命名空間, 被繼承`Class` 皆可存取
- `private protected` :
  - 類別: 自身`Class`, 被繼承`Class` 皆可存取
  - 成員: 自身`Class` 可存取, 被繼承`Class`無法存取

### 繼承(_Inheritance_)

- 類別 (而不是結構) 支援繼承的概念。
- 衍生自另一個類別的類別，稱為 基類，會自動包含基類的所有公用、受保護和內部成員，但建構函式和完成項除外。
- 類別可宣告為抽象，這表示其一或多個方法沒有任何實作。
  (雖然抽象類別無法直接具現化，但是它們可以做為其他能提供遺失實作之類別的基底類別。)
- 類別也可以宣告為密封(`sealed`)，以防止其他類別繼承它們。(類別可以藉由將本身或成員宣告為 [`sealed`](https://bit.ly/3OfgO2v) ，以防止其他類別繼承自它或其任何成員)

> 關係是一層一層繼承下來的, 舉例來說,手機可以通話、傳訊息，所以各品牌都是繼承 手機 的功能 並延伸自己的特色及功能，比如說 早期 3310 有基本手機功能且黑白畫面， 而目前 Iphone 有彩色畫面且還能上網

```cs
public class Program
{
    static void Main(string[] args)
    {
        var Phone_3310 = new Phone();
        Console.WriteLine("手機型號: 3310, 其功能如下");
        Phone_3310.Text();
        Phone_3310.Call();
        Console.ReadKey();
    }
    // Output: 手機型號: 3310, 其功能如下
    // 傳簡訊
    // 打電話
    public class Phone
    {
        public void Text(){
          Console.WriteLine("傳簡訊");
        }
        public void Call(){
          Console.WriteLine("打電話");
        };
    }
}
```

```cs
public class Program
{
    static void Main(string[] args)
    {
        var Phone_Iphone = new Iphone();
        Console.WriteLine("手機型號: Iphone, 其功能如下");
        Phone_Iphone.Text();
        Phone_Iphone.Call();
        Phone_Iphone.Internet();
        Phone_Iphone.OLED();
        Console.ReadKey();
    }
    // Output: 手機型號: 3310, 其功能如下
    // 傳簡訊
    // 打電話
    // 可上網
    // 彩色螢幕

    public class Phone
    {
        public void Text(){
          Console.WriteLine("傳簡訊");
        }
        public void Call(){
          Console.WriteLine("打電話");
        };
    }

    public class Iphone: Phone
    {
        public void Internet(){
            Console.WriteLine("可上網");
        }
        public void OLED(){
            Console.WriteLine("彩色螢幕");
        };
    }
}
```

### 多型(_Polymorphism_)

- 多型在希臘文中表示「多種形狀」
- 在執行階段，衍生類別物件可視為方法參數和集合或陣列等位置中的基底類別物件。 當這個多型發生時，物件的宣告型別與其執行時間型別不再相同。(比較文言文的說法:sweat:)
- 就是同一件事情，有需多不同的做法(比較白話文的說法:stuck_out_tongue:)

> 同一件事情，有需多不同的做法，舉個例子，如同 粽子，同是粽子有分南部粽、北部粽、東部粽、潮州粽...等不同做法。

1. `interface`

    ```cs
    // 介面多型
    public class Program
    {
        static void Main(string[] args)
        {
            I粽子 南部粽做法 = new 南部粽();
            南部粽做法.做法();// Output: 南部粽:米半熟~水煮
            I粽子 北部粽做法 = new 北部粽();
            北部粽做法.做法();// Output: 北部粽:油飯~蒸煮
            北部粽做法.評價();// Error: I粽子 未包含 評價 的定義(也就是說, 子類以父類建立時，只能以父類有的為主)
            Console.ReadKey();
        }
        
        public interface I粽子
        {
          void 做法();
        }

        public class 南部粽: I粽子{
          public void 做法(){
            Console.WriteLine("南部粽:米半熟~水煮");
          }
        }

        public class 北部粽: I粽子{
          public void 做法(){
            Console.WriteLine("北部粽:油飯~蒸煮");
          }
          public void 評價(){
            Console.WriteLine("好吃");
          }
        }
    }
    ```

2. `virual` method

    ```cs
    // virual method
    public class Program
    {
        static void Main(string[] args)
        {
            Coffee normalcafe = new Coffee();
            ShallowPeas sp = new ShallowPeas();
            DeepPeeledBeans dp = new DeepPeeledBeans();
            Coffee[] cafes = { normalcafe, sp, dp };
            foreach(var cafe in cafes){
              cafe.make();
            }
            Console.ReadKey();

            // Output:
            // Brewed and taste normal
            // Brewed and tasted sour
            // Brewed and tasted bitter
        }
        
        public class Coffee
        {
          public virual void make(){
            Console.WriteLine("Brewed and taste normal");
          }
        }

        public class ShallowPeas: Coffee{
          public override void make(){
            Console.WriteLine("Brewed and tasted sour");
          }
        }

        public class DeepPeeledBeans: Coffee{
          public override void make(){
            Console.WriteLine("Brewed and tasted bitter");
          }
        }
    }
    ```

3. 可以使用 `base` 取得父類方法或屬性

    ```cs
    public class Program
    {
        static void Main(string[] args)
        {
            Coffee normalcafe = new Coffee();
            Americano Acafe = new Americano();
            normalcafe.make();// Output: Brewed and taste normal
            Acafe.make();// Output: Brewed and taste normal
            Console.ReadKey();
        }
        
        public class Coffee
        {
          public virual void make(){
            Console.WriteLine("Brewed and taste normal");
          }
        }

        public class Americano: Coffee{
          public override void make(){
            base.make();
          }
        }
    }
    ```

4. Use `New` to hide the base class member

    ```cs
    // Use new to hide the base class member
    public class Program
    {
        static void Main(string[] args)
        {
            ShallowPeas sp = new ShallowPeas();
            sp.make();// Output: Brewed and tasted sour
            Coffee normalcafe = (Coffee)sp;
            normalcafe.make();// Output: Brewed and taste normal
            Console.ReadKey();
        }
        
        public class Coffee
        {
          public void make(){
            Console.WriteLine("Brewed and taste normal");
          }
        }

        public class ShallowPeas: Coffee{
          public new void make(){
            Console.WriteLine("Brewed and tasted sour");
          }
        }
    }
    ```

### 抽象化(_Abstraction_)

- 以特性來說,就是將需求轉為類別,而此類別可以包含狀態(_Property_)及行為(_Method_),前幾個特性皆有示例已包含抽象化特性

    ```cs

    public class Program
        {
            static void Main(string[] args)
            {
                Coffee cafe = new Coffee();
                cafe.from = "Ethiopia";
                cafe.type = "ShallowPeas"
                Console.WriteLine($"來自{cafe.from}的{cafe.type}咖啡");
                cafe.make();
                Console.ReadKey();

                // Output:
                // 來自Ethiopia的ShallowPeas咖啡
                // Brewed and tasted sour
            }
            
            public class Coffee
            {
              public string from { get; set; }
              public string type { get; set; }
              public void make(){
                Console.WriteLine("Brewed and taste sour");
              }
            }
        }

    ```

- `abstract` 抽象類別
  - 不能被實例化
  - 如不應該被實體化成一個物件，此類別就應該成為抽象類別
  - 子類必須要 `override` 方法才能使用
  
  ```cs
  public class Program
  {
      static void Main(string[] args)
      {
          var pg = new Lou();
          Console.WriteLine($"The Programming Language is {pg.language}");
          pg.Coding();
          Console.ReadKey();

          // Output:
          // The Programming Language is CSharp
          // 努力寫專案
      }
      
      public class Programer
      {
        public string language { get; set; }
        public abstract void Coding();
      }

      public class Lou: Programer{
        public string language { get; set; } => "CSharp";
        public override void Coding(){
          Console.WriteLine("努力寫專案");
        }
      }
  }
  ```

## 參考資料

- [MSDN - C# 文件](https://bit.ly/3QqyBWu)
- [YaYi - OOP三大特性：封裝、繼承、多型](https://bit.ly/3b1zZP3)
- [保持前進、持續優化程式碼內涵](https://bit.ly/3N06kTL)
- [數據交點文摘 - 物件導向程式設計四大支柱之一：封裝](https://bit.ly/3HDPfxN)
- [理工宅 - c# Public? Private? protected? 比較各種修飾詞存取範圍](https://bit.ly/3xy5WpU)
- [程式人生 - C#多型的實現：虛方法、抽象類、介面](https://bit.ly/3xWIsw2)
- [搞笑談軟工 - 什麼是物件導向（3）：Polymorphism](https://bit.ly/3tDge6H)
- [伊果的沒人看筆記本 - 菜雞與物件導向 (5): 多型](https://bit.ly/3mSa1jP)
- [伊果的沒人看筆記本 - 菜雞與物件導向 (6): 抽象、覆寫](https://bit.ly/3HwYVds)
- [菜鳥工程師 肉豬 - Java 什麼是多載(Overload), 覆寫(Override), 多型(Polymorphism)](https://bit.ly/3mWJzFO)

