# Rex.Utils.Core

## 简介
- 3.x 版本支持 `.NET Standard 2.1` 
- 2.x 版本支持 `.NET Standard 2.1` `.NET Framework 4.7`，包括开发中常用的扩展方法。

## 目录
* [更新日志](CHANGELOG.md "更新日志")（2020.5.1）
* [1.判断+检测](#1判断检测)
* [2.加密](#2加密)
* [3.类型转换](#3类型转换)
* [4.基于系统扩展](#4基于系统扩展)
* [5.文字处理](#5文字处理)
* [6.记录日志](#6记录日志)
* [7.配置文件读取](#7配置文件读取)

## 1.判断+检测

### 1.1 IsNull
- 判断是否为 null or empty，简化 string.IsNullOrEmpty
- 删除开头和结尾空白字符
- 支持已下类型：
  - `string`
  - `object`
  - `DataTable`
  - `DataRowCollection`
  - `DataRow`
  - `DataRow[]`
  - `DataSet`
  - `IEnumerable<T>`

*[C#]*

```csharp
object o = null;
o.IsNull(); // true

string str = null;
str.IsNull(); // true
"".IsNull();  // true
" ".IsNull(); // true

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

### 1.2 IsRange
支持检测 数值/时间/集合 是否在的范围内

*[C#]*

```csharp
var a = 1;
var b = 10;
var c = 5;
var d = 14;
c.IsRange(a, b); // true
d.IsRange(a, b); // false

var a = DateTime.Parse("2019/5/1");
var b = DateTime.Parse("2019/5/5");
var c = DateTime.Parse("2019/5/2");
var d = DateTime.Parse("2019/5/6");
c.IsRange(a, b); // true
d.IsRange(a, b); // false

var list = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
var val1 = 3;
var val2 = 55;
val1.IsRange(list); // true
val2.IsRange(list); // false
```

## 2.加密

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

## 3.类型转换

### 3.1 Bool
已下字符串会转换成 true
- `true`
- `yes`
- `1`
- `on`
- `是`

*[C#]*

```csharp
"true".ToBool(); // true
"yes".ToBool();  // true
"1".ToBool();    // true
"on".ToBool();   // true
"是".ToBool();   // true
```

### 3.2 Dictionary
- `ToDictionary` 目前只支持[枚举]转[字典]
- `AddOrReplace` 添加或替换已有内容
- `AddRange` 合并两个字典
- `GetValue` 从字典取值

*[C#]*

```csharp
enum MediaType { MP3 = 1,MP4,AVI,MOV,WMV,WAV }

var media = (new MediaType()).ToDictionary();

```

### 3.3 List
- 通过反射转 List ==> `ToListByReflect`
- 通过动态生成代码转 List ==> `ToListByDynamic`

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

### 3.4 Guid

*[C#]*

```csharp
var gid = str.ToGuid();
```

### 3.5 Number
  * `ToInt16`
  * `ToInt32`
  * `ToInt64`
  * `ToFloat`
  * `ToByte`
  * `ToDecimal`
  * `ToDouble`

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
  * `ToDateTime` 字符串转时间
  * `ToUnixTimestamp` 时间格式转换为 Unix 时间戳格式
  * `EndOfDay`  一天末尾时间
  * `EndOfMonth`  一个月末尾时间
  * `EndOfWeek`  一周末尾时间
  * `EndOfYear` 一年末尾时间
  * `FirstDayOfWeek` 一周的第一天
  * `GetAge` 根据出生日期计算年龄
  * `GetDateDiff` 日期差计算
  * `GetDays` 获取一个月有多少天
  * `GetFriendlyString` 友好时间
  * `GetWeekdays` 返回两个时间内工作日天数
  * `GetWeekends` 返回两个时间内周末天数
  * `GetWeekNumber` 获取日期是一年中第几个星期
  * `IsToday` 日期是否是今天
  * `IsWeekDay` 是否是工作日
  * `IsWeekEnd` 是否是周末

*[C#]*

```csharp
"2019/5/1".ToDateTime();    // 2019/5/1 00:00:00

// Excel 日期转换，如 Excel 日期 43799.9820949074 ==> 2019/11/30 23:34:13，失败时返回 null
DateTime? dts = (43799.9820949074).ToDateTime();

// Unix 时间戳 ==> DateTime
1556687655000.ToDateTime(); // 2019/5/1 13:14:15

DateTime.Parse("2019/5/1 13:14:15").ToUnixTimestamp(); // 1556687655000
DateTime.Now.ToUnixTimestamp() //

//一天末尾时间
DateTime.Now.EndOfDay(); // 2019/7/19 23:59:59
//一个月末尾时间
DateTime.Now.EndOfMonth(); // 2019/7/31 23:59:59
//一周末尾时间
DateTime.Now.EndOfWeek(); // 2019/7/21 23:59:59
//一年末尾时间
DateTime.Now.EndOfYear(); // 2019/12/31 23:59:59
//工作周的第一天（星期一）
DateTime.Now.FirstDayOfWeek(); // 2019/7/14 0:00:00（周日）

//根据出生日期计算年龄
"2000/7/19".ToDateTime().GetAge(); // 19

//日期差计算
new DateTime(2000, 7, 19).GetDateDiff(DateTime.Parse("2019-7-19"), Extend.Enum.DateTimePart.Year); // 19

//获取一个月有多少天
DateTime.Now.GetDays(); // 30

//友好时间
DateTime.Now.AddDays(-3).GetFriendlyString();
// 32秒前
// 1分钟之前
// 3分钟
// 1小时前
// 3小时前
// 昨天
// 3天之前
// 1个月之前
// 3月之前
// 3年前

//返回两个时间内工作日天数
var t1 = new DateTime(2019, 7, 1);
var t2 = new DateTime(2019, 7, 10);
t1.GetWeekdays(t2); // 7

//返回两个时间内周末天数
var t1 = new DateTime(2019, 7, 1);
var t2 = new DateTime(2019, 7, 19);
t1.GetWeekends(t2); // 4


//获取日期是一年中第几个星期（星期一为本周第一天）
DateTime.Now.GetWeekNumber(); // 29

//日期是否是今天
DateTime.Now.IsToday(); // true | false

//是否是工作日
DateTime.Now.IsWeekDay(); // true | false

//是否是周末（周六日）
DateTime.Now.IsWeekEnd(); // true | false

```

### 3.7 JSON
  * `ToJson`
  * `ToDeserializeObject`

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

### 3.8 TitleCase
*[C#]*

```csharp
var str1 = "codec_long_name".ToTitleCase();  // "Codec_Long_Name"
var str2 = "codeclongname".ToTitleCase();    // "Codeclongname"
var str3 = "codec long name".ToTitleCase();  // "Codec Long Name"
var str4 = "一个.net core平台".ToTitleCase(); // "一个.Net Core平台"
```

### 3.9 泛型集合 与 DataTable 互转
  * `ToDataTable<T>`
  * `ToListByReflect`
  * `ToListByDynamic` 

*[C#]*

```csharp
var dic = new Dictionary<int, int?>();
dic.Add(1, 123);
dic.Add(2, 234);
dic.Add(3, null);
var table = dic3.ToDataTable();
// table.Columns.Count ==> 2
// table.Rows[0][0] ==> 1
// table.Rows[0][1] ==> 123
// table.Rows[1][0] ==> 2
// table.Rows[1][1] ==> 234
// table.Rows[2][0] ==> 3
// table.Rows[2][1] ==> DBNull.Value


var arr = new[] { 1, 2, 3, 4 };
var table = arr.ToDataTable();
// table.Columns.Count ==> 1
// table.Rows[0][0] ==> 1
// table.Rows[1][0] ==> 2
// table.Rows[2][0] ==> 3
// table.Rows[3][0] ==> 4


var list = new List<Demo>
{
    new Demo {PackageId = "1", ReleaseDate = DateTime.Parse("2019-12-01"), Version = "1.1"},
    new Demo {PackageId = "2", ReleaseDate = DateTime.Parse("2019-12-02"), Version = "1.2"},
    new Demo {PackageId = "3", ReleaseDate = DateTime.Parse("2019-12-03"), Version = "1.3"},
};
table = list.ToDataTable();
// table.Columns.Count ==> 3
// table.Rows[0]["PackageId"] ==> "1"
// table.Rows[0]["ReleaseDate"] ==> 2019-12-01 00:00:00
// table.Rows[0]["Version"] ==> "1.1"
// table.Rows[1]["PackageId"] ==> "2"
// table.Rows[1]["ReleaseDate"] ==> 2019-12-02 00:00:00
// table.Rows[1]["Version"] ==> "1.2"
// table.Rows[2]["PackageId"] ==> "3"
// table.Rows[2]["ReleaseDate"] ==> 2019-12-03 00:00:00
// table.Rows[2]["Version"] ==> "1.3"

var d1 = table.ToListByDynamic<Demo>();  // d1 == list
var d2 = table.ToListByReflect<Demo>();  // d2 == list

```
## 4.基于系统扩展

#### 4.1 Append/AppendBefore

*[C#]*

```csharp
var str1 = "获取当前";
var str2 = "System.String";
var str3 = "对象中的字符数";
var num1 = 123;
var str = str1.Append(str2).Append(str3).Append(num1);   // "获取当前System.String对象中的字符数123"

var b = "bbb".Append(111).AppendBefore("@@"); // @@bbb111
```

#### 4.2 Lengths

*[C#]*

```csharp
var str = "获取当前System.String对象中的字符数";

str.Lengths(); // 35
str.Length;    // 24
```

#### 4.3 Trims

*[C#]*

```csharp
var val = str.Trims();       // 等同于 str?.Trim();

var str = "获取当前System.String对象中的字符数";
var val = str.Trims('获', '数');  // "取当前System.String对象中的字符"

var val = str.Trims(null);  // null
```

#### 4.4 Replaces

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

#### 4.5 Splits
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

#### 4.6 Joins

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

#### 4.7 StringCut
字符串裁剪

*[C#]*

```csharp
var str = "Newtonsoft.Json";
str.StringCut("New", "."); // tonsoft
```


#### 4.8 Strings
- 删除开头和结尾空白字符
- 支持已下类型：
  - `DateTime`
  - `bool`
  - `DBNull.Value`
  - `null`
  - `string`
  - `DataTable`
  - `DataRow`
  - `DataRow[]`
  - `T[]`

*[C#]*

```csharp
// DateTime
DateTime? dt1 = null;
dt1.ToStrings();  // null

var dt2 = DateTime.Parse("2019/5/1");
dt2.ToStrings();             // "2019/05/01 00:00:00"  默认格式：yyyy/MM/dd HH:mm:ss
dt2.ToStrings("dd/MM/yyyy"); // "01/05/2019"

// bool
true.ToString();  // "True" 系统ToString
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

// T[]
var arr = new[] { 1, 2, 3, 4, 5 };
var val = arr.ToStrings(",");  // "1,2,3,4,5"

```

#### 4.9 `IEnumerable<T>` Add/IndexOf/Distinct

*[C#]*

```csharp
var list = new List<int> { 1, 2, 3 };

var q1 = list
    .Select(x => x)
    .Add(11, 12)
    .Add(new[] { 13, 14 })
    .Add(list.Select(x => x));
// 1  2  3  11  12  13  14  1  2  3

IEnumerable<int> q2 = null;
var q3 = q2.Add(list.Select(x => x));
// 1  2  3

IEnumerable<int> q4 = null;
var q5 = q4.Add(null);
// null

```
## 5.文字处理
> for [ToolGood.Words](https://www.nuget.org/packages/ToolGood.Words/ "ToolGood.Words")

### 5.1 半角 ==> 转换 <== 全角

- `ToSBC`
- `ToDBC`

*[C#]*

```csharp
str.ToSBC(); // "abcABC123" ==> "ａｂｃＡＢＣ１２３"
str.ToDBC(); // "ａｂｃＡＢＣ１２３" ==> "abcABC123"
```

### 5.2 数字 ==> 转换 <== 中文大写

- `ToChineseRMB`
- `ToNumber`

*[C#]*

```csharp
double val = 123.45;
val.ToChineseRMB(); // "123.45" ==> "壹佰贰拾叁元肆角伍分"
str.ToNumber();     // "壹佰贰拾叁元肆角伍分" ==> "123.45"
```

### 5.3 简体 ==> 转换 <== 繁体

- `ToSimplifiedChinese`
- `ToTraditionalChinese`

*[C#]*

```csharp
str.ToTraditionalChinese(); // "简体转换繁体" ==> "簡體轉換繁體"
str.ToSimplifiedChinese();  // "簡體轉換繁體" ==> "简体转换繁体"
```

### 5.4 判断输入是否为中文/英文
- `HasChinese`
- `IsAllChinese`
- `HasEnglish`
- `IsAllEnglish`

*[C#]*

```csharp
str.HasChinese();   // 判断输入是否为中文  ,中文字符集为[0x4E00,0x9FA5]
str.IsAllChinese(); // 判断输入是否全为中文,中文字符集为[0x4E00,0x9FA5]
str.HasEnglish();   // 判断是否含有英语
str.IsAllEnglish(); // 判断是否全部英语
```

### 5.5 拼音
- `GetFirstPinYin`
- `GetPinYin`
- `GetPinYinSpace`
- `GetAllPinYin`

*[C#]*

```csharp
str.GetFirstPinYin(); // 获取首字母，中文字符集为[0x4E00,0x9FA5]，我爱中国 ==> "WAZG"
str.GetPinYin();      // 获取拼音全拼,支持多音,中文字符集为[0x4E00,0x9FA5]，我爱中国 ==> "WoAiZhongGuo"
str.GetPinYinSpace(); // 获取拼音全拼,支持多音,中文字符集为[0x4E00,0x9FA5]，我爱中国 ==> "Wo Ai Zhong Guo"
str.GetAllPinYin();   // 获取所有拼音,中文字符集为[0x4E00,0x9FA5]，传 ==> "Chuan", "Zhuan"
```

## 6.记录日志
> for [log4net](https://www.nuget.org/packages/log4net/ "log4net")

- `Error`
- `Fatal`
- `Warn`
- `Info`
- `Debug`

*AppSettings.json*
```JSON
{
  "Appsettings": {
    "Log4NetPath": "/App_Data/log4net.config",
    "CacheTime": 3600,
    }
}
```

*web.config*
```xml
<appSettings>
   <add key="Log4NetPath" value="/App_Data/log4net.config" />
</appSettings>
```


*[C#]*

```csharp
// LogHelper.Error(System.Reflection.MethodBase.GetCurrentMethod(), ex, "ABC", "123");
// LogHelper.Fatal(System.Reflection.MethodBase.GetCurrentMethod(), ex);
// LogHelper.Warn(System.Reflection.MethodBase.GetCurrentMethod(), ex);
// LogHelper.Info(System.Reflection.MethodBase.GetCurrentMethod(), ex);
// LogHelper.Debug(System.Reflection.MethodBase.GetCurrentMethod(), ex);

// ex ==> Exception or IEnumerable<string> or string

public void Test() {
    try {
        abc();
    }
    catch (Exception ex) {
        LogHelper.Error(System.Reflection.MethodBase.GetCurrentMethod(), ex, "Error","测试");
    }
}

public static void abc() {
    throw new Exception("test");
}
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

## 7.配置文件读取（带缓存）
- GetValue

- Framework：配置在 web.config
- .Net Core：可将 AppSettings.json 文件放置于根目录

*AppSettings.json*
```JSON
{
  "Appsettings": 
    {
        "Log4NetPath": "/App_Data/log4net.config",
        "CacheTime": 3600,
        "DESKey": "DES-keys"
    }
}
```

*web.config*
```xml
<appSettings>
   <add key="Log4NetPath" value="/App_Data/log4net.config" />
   <add key="CacheTime" value="3600" />
   <add key="DESKey" value="DES-keys"/>
</appSettings>
```

*[C#] .Net Framework*

```csharp
string abc = AppSettings.GetValue("Log4NetPath");   // "/App_Data/log4net.config"
int number = AppSettings.GetValue<int>("CacheTime");   // 3600
```

*[C#] .Net Core*
```csharp
string abc = AppSettings.GetValue("AppSettings:Log4NetPath");   // "/App_Data/log4net.config"
int number = AppSettings.GetValue<int>("AppSettings:CacheTime");   // 3600
```

