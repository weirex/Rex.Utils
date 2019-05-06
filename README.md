# Rex.Utils

## 简介
基于框架 `.NET Framework 4.7`，包含一系列快速开发中经常用到的 Utility 辅助功能。

## 1. 验证判断

### 1.1 IsNull
判断是否为空


### 1.2 IsInRange
支持检测 数值/时间/集合 是否在的范围内

*[C#]*

```csharp
var a = 1;
var b = 10;
var c = 5;
var d = 14;
c.IsInRange(a, b); // true
d.IsInRange(a, b); // false

var a = DateTime.Parse("2019/5/1");
var b = DateTime.Parse("2019/5/5");
var c = DateTime.Parse("2019/5/2");
var d = DateTime.Parse("2019/5/6");
c.IsInRange(a, b); // true
d.IsInRange(a, b); // false

var list = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
var val1 = 3;
var val2 = 55;
val1.IsInRange(list); // true
val2.IsInRange(list); // false
```


## 2. 基础加密

### 2.1 Base64

*[C#]*

```csharp
// 默认编码 utf-8
var b64 = val.Base64Encode();
var str = b64.Base64Decode();

// 自定义编码
var b64 = val.Base64Encode(Encoding.Default);
var str = b64.Base64Decode(Encoding.Default);
```

### 2.2 DESEncrypt

*[AppSettings]*

```xml
<appSettings>
  <add key="DESKey" value="DES-keys"/>
</appSettings>
```

*[C#]*

```csharp
// 默认自动读取 AppSettings 的 DESKey
var pwd = val.DESEncrypt();
var str = pwd.DESDecrypt();

// 自定义加密 key
var key="DES-keys";
var pwd = val.DESEncrypt(key);
var str = pwd.DESDecrypt(key);
```

### 2.3 MD5

*[C#]*

```csharp
var md5 = str.MD5();
```

### 2.4 SHA1

*[C#]*

```csharp
var sha1 = str.SHA1();
```


## 3. 类型转换

### 3.1 ToBool
已下字符串会转换成 true
- true
- yes
- 1
- on
- 是

### 3.2 ToDictionary
目前只支持 枚举 转 字典

*[C#]*

```csharp
enum MediaType { MP3 = 1,MP4,AVI,MOV,WMV,WAV }

var media = (new MediaType()).ToDictionary();
```

### 3.3 ToList
- ToListByReflect：通过反射转 List
- ToListByDynamic：通过动态生成代码转 List

*[C#]*

```csharp
var dt = new DataTable();
dt.Columns.Add("PackageId", typeof(string));
dt.Columns.Add("Version", typeof(string));
dt.Columns.Add("ReleaseDate", typeof(DateTime));
dt.Rows.Add("Newtonsoft.Json", "11.0.1", new DateTime(2018, 2, 17));
dt.Rows.Add("Newtonsoft.Json", "10.0.3", new DateTime(2017, 6, 18));

var list1 = dt.ToListByReflect<Demo>();
var list2 = dt.ToListByDynamic<Demo>();
```
> 数据量较大时 ToListByDynamic 较快，数据量较小时 ToListByReflect 较快，还在测试中暂时无法保证性能准确性。

### 3.4 ToGuid

*[C#]*

```csharp
var gid = str.ToGuid();
```

### 3.5 Number
  * ToInt16
  * ToInt32
  * ToInt64
  * ToFloat
  * ToByte
  * ToDecimal
  * ToDouble

*[C#]*

```csharp
object obj = 1;
string str = "2";

var a1 = obj.ToInt16();
var a2 = str.ToInt16();

var b1 = obj.ToInt32();
var b2 =str.ToInt32();

var c1 = obj.ToInt64();
var c2 = str.ToInt64();

var d1 = obj.ToFloat();
var d2 = str.ToFloat();

var e1 = obj.ToByte();
var e2 = str.ToByte();

var f1 = obj.ToDecimal();
var f2 = str.ToDecimal();

var g1 = obj.ToDouble();
var g2 = str.ToDouble();
```

### 3.6 Time
  * ToDateTime
  * ToUnixTimestamp
  * GetTimeFormat
  * GetTime

### 3.7 JSON
  * ToJson
  * ToDeserializeObject

*[C#]*

```csharp
var m = new Movie();
m.Name = "Rex";
m.ReleaseDate = DateTime.Parse("2019-5-1");
m.Genres = new[] { "Action", "Comedy" };
var str = m.ToJson();

var json = @"{
  'Name': 'Rex',
  'ReleaseDate': '2019-5-1',
  'Genres': [
    'Action',
    'Comedy'
  ]
}";
var model = json.ToDeserializeObject<Movie>();
```

### 3.8 基于系统扩展
  * ToStrings
  * ToTitleCase
  * ToDBValue
  * Lengths
  * Append
  * Trims
  * Replaces
  * Substrings
  * Splits
  * TrimEnds
  * TrimStarts
  * Joins


## 4. 文字处理
> for [ToolGood.Words](https://www.nuget.org/packages/ToolGood.Words/ "ToolGood.Words")

### 4.1 半角 ==> 转换 <== 全角

- ToSBC
- ToDBC

*[C#]*

```csharp
str.ToSBC(); // abcABC123 ==> ａｂｃＡＢＣ１２３
str.ToDBC(); // ａｂｃＡＢＣ１２３ ==> abcABC123
```

### 4.2 数字 ==> 转换 <== 中文大写

- ToChineseRMB
- ToNumber

*[C#]*

```csharp
double val = 123.45;
val.ToChineseRMB(); // 123.45 ==> 壹佰贰拾叁元肆角伍分
str.ToNumber();     // 壹佰贰拾叁元肆角伍分 ==> 123.45
```

### 4.3 简体 ==> 转换 <== 繁体

- ToSimplifiedChinese
- ToTraditionalChinese

*[C#]*

```csharp
str.ToTraditionalChinese(); // 简体转换繁体 ==> 簡體轉換繁體
str.ToSimplifiedChinese();  // 簡體轉換繁體 ==> 简体转换繁体
```

### 4.4 判断输入是否为中文/英文
- HasChinese
- IsAllChinese
- HasEnglish
- IsAllEnglish

*[C#]*

```csharp
str.HasChinese();   // 判断输入是否为中文  ,中文字符集为[0x4E00,0x9FA5]
str.IsAllChinese(); // 判断输入是否全为中文,中文字符集为[0x4E00,0x9FA5]
str.HasEnglish();   // 判断是否含有英语
str.IsAllEnglish(); // 判断是否全部英语
```

### 4.5 拼音
- GetFirstPinYin
- GetPinYin
- GetPinYinSpace
- GetAllPinYin

*[C#]*

```csharp
str.GetFirstPinYin(); // 获取首字母，中文字符集为[0x4E00,0x9FA5]，我爱中国 ==> WAZG
str.GetPinYin();      // 获取拼音全拼,支持多音,中文字符集为[0x4E00,0x9FA5]，我爱中国 ==> WoAiZhongGuo
str.GetPinYinSpace(); // 获取拼音全拼,支持多音,中文字符集为[0x4E00,0x9FA5]，我爱中国 ==> Wo Ai Zhong Guo
str.GetAllPinYin();   // 获取所有拼音,中文字符集为[0x4E00,0x9FA5]，传 ==> Chuan, Zhuan
```


## 5. 记录日志 LogHelper
> for [log4net](https://www.nuget.org/packages/log4net/ "log4net")

- Error
- Fatal
- Warn
- Info
- Debug

*[C#]*

```csharp
LogHelper.Error(System.Reflection.MethodBase.GetCurrentMethod(), ex);
LogHelper.Fatal(System.Reflection.MethodBase.GetCurrentMethod(), ex);
LogHelper.Warn(System.Reflection.MethodBase.GetCurrentMethod(), ex);
LogHelper.Info(System.Reflection.MethodBase.GetCurrentMethod(), ex);
LogHelper.Debug(System.Reflection.MethodBase.GetCurrentMethod(), ex);

// ex ==> Exception or IEnumerable<string> or string
```


## 6. 配置文件读取 AppSettings
- GetValue

*[AppSettings]*

```xml
<appSettings>
   <add key="abc" value="abc"/>
   <add key="number" value="123456"/>
</appSettings>
```

*[C#]*

```csharp
var abc = AppSettings.GetValue("abc");
var number = AppSettings.GetValue<int>("number");
```
