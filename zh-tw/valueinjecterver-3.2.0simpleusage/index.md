# ValueInjecter@3.2.0 簡單用法

## 基本用法

1. 一般對應
   - `var mapper = Mapper.Map<TargetType>(customer);`
   - `var mapper = Mapper.Map<FromType, TargetType>(customer);`
2. 客製化對應

   ```cs
   Mapper.AddMap<FromType, TargetType>(src =>
   {
    var res = new TargetType();
    res.InjectFrom(src); // 對應相同名字及型別的屬性
    res.FullName = src.FirstName + " " + src.LastName;
    return res;
   });
   ```

3. `InjectFrom` 的用法
   `InjectFrom<TInjection>(source)`用於使用約定映射，當未指定 `TInjection` 時，它將對應具有完全相同名稱和類型的屬性

   ```cs
   target.InjectFrom(source);
   target.InjectFrom<Injection>(source);
   target.InjectFrom(new Injection(parameters), source);
   target.InjectFrom<Injection>();
   ```

## 專案上使用遇到的問題

- 當 `List` 需要轉換對應時

  - 問題:

  ```cs
    public async Task<List<EmplyeeListDto>> GetAllEmployeeAsync(CancellationToken token)
   {
      var query = await _context.Emplyees.OrderByDescending(e => e.Jointime).ToListAsync(token);
      var data = Mapper.map<List<EmplyeeListDto>>(query);//此方式是無法對應任何屬性的
      return data;
   }
  ```

  - 解法:

  ```cs
   public async Task<List<EmplyeeListDto>> GetAllEmployeeAsync(CancellationToken token)
   {
      var query = await _context.Employees.OrderByDescending(e => e.Jointime).ToListAsync(token);
      var data = query.Select(q => new EmplyeeListDto().InjectFrom(q)).Cast<EmplyeeListDto>().ToList();//改用此方式即可,需使用Cast<T>才會是正確的屬性
      return data;
   }
  ```

  如沒使用`Cast<T>`則會變成 Object 屬性:
  ![image](https://user-images.githubusercontent.com/33840759/133583605-a7a72756-84ca-4513-a5c5-7de9585abdae.png)

## 參考資料

1. [GitHub - ValueInjecter](https://tinyurl.com/yzy9hqsf)
2. [StackOverFlow](https://tinyurl.com/yjkfnjqo)

