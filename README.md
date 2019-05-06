# Rex.Utils

## 简介
基于框架 `.NET Framework 4.7`，包含一系列快速开发中经常用到的 Utility 辅助功能。

## 1. 验证判断

### 1.1 IsNull
- 判断是否为 null or empty，简化 string.IsNullOrEmpty
- 删除开头和结尾空白字符
- 支持已下类型：
 - string
 - object
 - DataTable
 - DataRowCollection
 - DataRow
 - DataRow[]
 - DataSet
 - IEnumerable<T>

*[C#]*

```csharp
object o = null;
o.IsNull(); // true

string str = null;
str.ToString(); // true
"".ToString();  // true
" ".ToString(); // true

var dt = new DataTable();
dt.IsNull();          // true
dt.Rows.IsNull();     // true
dt.Select().IsNull(); // true

DataRow row = dt.NewRow();
row.IsNull(); // false

var ds = new DataSet();
ds.Tables.Add(new DataTable());
ds.IsNull(); // false
ds = null;
ds.IsNull(); // true

var list = new List<string>();
list.IsNull(); // false
list = null;
list.IsNull(); // true
```

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

*[C#]*

```csharp
var t1 = "2019/5/1".ToDateTime();    // 2019/5/1
var t2 = 1556687655000.ToDateTime(); // 2019/5/1 13:14:15

var n1 = DateTime.Parse("2019/5/1 13:14:15").ToUnixTimestamp(); // 1556687655000
var n2 = DateTime.Now.ToUnixTimestamp() //

```

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
#### 3.8.1 ToTitleCase

*[C#]*

```csharp
var str1 = "codec_long_name".ToTitleCase();  // "Codec_Long_Name"
var str2 = "codeclongname".ToTitleCase();    // "Codeclongname"
var str3 = "codec long name".ToTitleCase();  // "Codec Long Name"
var str4 = "一个.net core平台".ToTitleCase(); // "一个.Net Core平台"
```
#### 3.8.2 Append

*[C#]*

```csharp
var str1 = "获取当前";
var str2 = "System.String";
var str3 = "对象中的字符数";
var num1 = 123;
var str = str1.Append(str2).Append(str3).Append(num1);   // "获取当前System.String对象中的字符数123"
```

#### 3.8.3 Lengths

*[C#]*

```csharp
var str = "获取当前System.String对象中的字符数";

str.Lengths(); // 35
str.Length;    // 24
```

#### 3.8.4 ToStrings
- 删除开头和结尾空白字符
- 支持已下类型：
 - DateTime
 - bool
 - DBNull.Value
 - null
 - string
 - DataTable
 - DataRow
 - DataRow[]

*[C#]*

```csharp
// DateTime
DateTime? dt1 = null;
dt1.ToStrings();  // null

var dt2 = DateTime.Parse("2019/5/1");
dt2.ToStrings();             // "2019/05/01 00:00:00"  默认格式：yyyy/MM/dd HH:mm:ss
dt2.ToStrings("dd/MM/yyyy"); // "01/05/2019"

// bool
true.ToString();  // "True"
true.ToStrings(); // "true"

// DBNull.Value  or  null
object o = DBNull.Value;
o.ToStrings();    // null
o = null;
o.ToStrings();    // null

// string
var str = " abc ";
str.ToStrings();  // "abc";

// DataTable
var dt = new DataTable();
dt.Columns.Add("PackageId", typeof(string));
dt.Columns.Add("Version", typeof(string));
dt.Columns.Add("ReleaseDate", typeof(DateTime));
dt.Rows.Add("Newtonsoft.Json", "11.0.1", new DateTime(2018, 2, 17));
dt.Rows.Add("Newtonsoft.Json", "10.0.3", new DateTime(2017, 6, 18));

dt.ToStrings(0, 1);         // "11.0.1"
dt.ToStrings(0, "Version"); // "11.0.1"

// DataRow
dt.Rows[0].ToStrings();            // "Newtonsoft.Json"
dt.Rows[0].ToStrings("PackageId"); // "Newtonsoft.Json"

// DataRow[]
DataRow[] rows = dt.Select();
rows.ToStrings(1, 2);           // "2017/6/18 0:00:00"
rows.ToStrings(1, "PackageId"); // "Newtonsoft.Json"
```

#### 3.8.5 Trims

*[C#]*

```csharp
var val = str.Trims();       // 等同于 str?.Trim();

var str = "获取当前System.String对象中的字符数";
var val = str.Trims('获', '数');  // "取当前System.String对象中的字符"

var val = str.Trims(null);  // null
```

#### 3.8.6 Replaces

*[C#]*

```csharp
var str = "获取当前System.String对象中的字符数";

str.Replaces("S");                          // "获取当前ystem.tring对象中的字符数"
str.Replaces("S", "A");                     // "获取当前Aystem.Atring对象中的字符数"
str.Replaces(new[] { "S", "字符数" }, "@"); // "获取当前@ystem.@tring对象中的@"
```

#### 3.8.7 Substrings
- 截取字符串，区分中英文字符长度，中文按1:2，英文按1:1 计算长度

*[C#]*

```csharp
var str = "获取当前System.String对象中的字符数";

str.Substrings(7);         // "获取当"
str.Substrings(11);        // "获取当前Sys"
str.Substrings(11, "..."); // "获取当前Sys..."
```

#### 3.8.8 Splits
- 返回值不包括包含空字符串的数组元素
- 数组每项会删除开头和结尾空白字符

*[C#]*

```csharp
string str = "System.String";
str.Splits(null); // null

str = null;
str.Splits("s"); // null

str.Splits("");  // [ "System.String" ]
str.Splits("s"); // [ "Sy", "tem.String" ]

str = "/Sys//tem/ /.String";
str.Splits("/"); // [ "Sys", "tem", "", ".String" ]
str.Split('/');  // [ "", "Sys", "", "tem", " ", ".String" ]
```

#### 3.8.9 Joins

*[C#]*

```csharp
var list = new List<string>();
list.Joins("_");  // string.Empty  ( "" )

list = null;
list.Joins("_"); // null

list = new List<string>();
list.Add("A");
list.Add("B");
list.Add("C");
list.Joins("_"); // "A_B_C"
```

#### 3.8.10 StringCut
字符串裁剪

*[C#]*

```csharp
var str = "Newtonsoft.Json";
str.StringCut("New", "."); // tonsoft
```

## 4. 文字处理
> for [ToolGood.Words](https://www.nuget.org/packages/ToolGood.Words/ "ToolGood.Words")

### 4.1 半角 ==> 转换 <== 全角

- ToSBC
- ToDBC

*[C#]*

```csharp
str.ToSBC(); // "abcABC123" ==> "ａｂｃＡＢＣ１２３"
str.ToDBC(); // "ａｂｃＡＢＣ１２３" ==> "abcABC123"
```

### 4.2 数字 ==> 转换 <== 中文大写

- ToChineseRMB
- ToNumber

*[C#]*

```csharp
double val = 123.45;
val.ToChineseRMB(); // "123.45" ==> "壹佰贰拾叁元肆角伍分"
str.ToNumber();     // "壹佰贰拾叁元肆角伍分" ==> "123.45"
```

### 4.3 简体 ==> 转换 <== 繁体

- ToSimplifiedChinese
- ToTraditionalChinese

*[C#]*

```csharp
str.ToTraditionalChinese(); // "简体转换繁体" ==> "簡體轉換繁體"
str.ToSimplifiedChinese();  // "簡體轉換繁體" ==> "简体转换繁体"
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
str.GetFirstPinYin(); // 获取首字母，中文字符集为[0x4E00,0x9FA5]，我爱中国 ==> "WAZG"
str.GetPinYin();      // 获取拼音全拼,支持多音,中文字符集为[0x4E00,0x9FA5]，我爱中国 ==> "WoAiZhongGuo"
str.GetPinYinSpace(); // 获取拼音全拼,支持多音,中文字符集为[0x4E00,0x9FA5]，我爱中国 ==> "Wo Ai Zhong Guo"
str.GetAllPinYin();   // 获取所有拼音,中文字符集为[0x4E00,0x9FA5]，传 ==> "Chuan", "Zhuan"
```


## 5. 记录日志 LogHelper
> for [log4net](https://www.nuget.org/packages/log4net/ "log4net")

- Error
- Fatal
- Warn
- Info
- Debug

*[AppSettings]*

```xml
<appSettings>
   <add key="Log4NetPath" value="/App_Data/log4net.config" />
</appSettings>
```

*[log4net.config]*

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler,log4net" />
  </configSections>
  <log4net>
    <root>
      <level value="All" />
      <appender-ref ref="RollingLogFileAppender" />
    </root>
    <appender name="RollingLogFileAppender" type="log4net.Appender.RollingFileAppender">
      <encoding value="utf-8" />
      <file value="C:\Log\" />
      <appendToFile value="true" />
      <maxSizeRollBackups value="30" />
      <maximumFileSize value="5MB" />
      <lockingModel type="log4net.Appender.FileAppender+InterProcessLock" />
      <rollingStyle value="Composite" />
      <staticLogFileName value="false" />
      <datePattern value="yyyy-MM-dd'.log'" />
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="记录时间: %date%n线程ID: [%thread]%n日志级别: %-5level%n所在类: %logger property:[%property{NDC}] -%n日志描述: %n%message%n%n%n" />
      </layout>
    </appender>
  </log4net>
</configuration>
```

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
