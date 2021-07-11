# Rex.Utils

## 简介
- 扩展类库包括：类型转换、类型检测、加密/解密、日志、常用扩展方法、文字处理、配置文件读取等。
- 支持 `.NET 5.0` `.NET Framework 4.7`。

## 目录
* [更新日志（2021.07.10）](CHANGELOG.md "更新日志")
* [1.读取 `appsettings.json`](#1读取-appsettingsjson)
* [2.Convert 类型转换](#2convert-类型转换)
* [3.Linq 扩展](#3linq-扩展)
* [4.加密](#4加密)
* [5.基于系统方法的扩展](#5基于系统方法的扩展)
* [6.验证与判断](#6验证与判断)
* [7.其他](#7其他)


## 1.读取 appsettings.json
- `AppSettings.GetValue( )`
- `.NET Core` 读取 `appsettings.json`
- `.NET Framework` 读取 `web.config`
- key 不分区大小写
----

*AppSettings.json*
```JSON
{
  "AppSettings": {
    "Origins": [ "*" ],
    "Headers": [ "*" ],
    "Methods": [ "*" ],
    "CacheTime": 7200,
    "DESKey": "123456!@#$%^",
    "Log4NetPath": "/App_Data/log4net.config"
  }
}
```


*web.config*
- `.NET Framework` 仅支持数值类型、字符串类型

```xml
<appSettings>
   <add key="Origins" value="*"/>
   <add key="Headers" value="*"/>
   <add key="Methods" value="*"/>
   <add key="CacheTime" value="7200" />
   <add key="DESKey" value="123456!@#$%^"/>
   <add key="Log4NetPath" value="/App_Data/log4net.config" />
</appSettings>
```


*[C#]*

```csharp
string path = AppSettings.GetValue("Log4NetPath");   // "/App_Data/log4net.config"

string path = AppSettings.GetValueToPath("Log4NetPath");  // "C:\\Users\\......\\bin\\Debug\\net5.0\\App_Data\\log4net.config"

int t = AppSettings.GetValue<int>("CacheTime");  // 7200

int t = AppSettings.GetValue<int>("Time", 3600);  //key不存在，返回默认值 3600

// .NET Core
string[] origins = AppSettings.GetValue<string[]>("arr"); // string[] { "aa", "bb", "cc" }

```
----

#### .Net Core 支持泛型序列化
- `AppSettings.GetAppSettings<T>( key, path )`
- 可继承基类进行扩展

```csharp
public class AppSettingsBaseEntity {
    public string[] Origins { get; set; }
    public string[] Headers { get; set; }
    public string[] Methods { get; set; }
    public string DESKey { get; set; }
    public string Log4NetPath { get; set; }
    public int CacheTime { get; set; }
}

var app = AppSettings.GetAppSettings<AppSettingsBaseEntity>("AppSettings", "appsettings.json");

```
----


## 2.Convert 类型转换
- [1.Boolean](#21-boolean)
- [2.DataTable 与 List 转换](#22-tabletolist)
- [3.Dictionary](#23-dictionary)
- [4.Enum](#24-enum)
- [5.Guid](#25-guid)
- [6.Number](#26-number)
- [7.JSON](#27-json)
- [8.Stream](#28-stream)
- [9.String](#29-string)
- [10.Time](#210-time)
- [11.Words](#211-words)
----

### 2.1 Boolean
- 默认支持以下内容的转换
  - `true`
  - `yes`
  - `1`
  - `on`
  - `是`

```csharp
"true".ToBool(); // true
"yes".ToBool();  // true
"1".ToBool();    // true
"on".ToBool();   // true
"是".ToBool();   // true

bool? v = null;
v.ToBool() // false
```
----


### 2.2 TableToList
- 通过反射转 List ==> `ToListByReflect`
- 通过动态生成代码转 List ==> `ToListByDynamic`

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
> 数据量较大时 ToListByDynamic 较快，数据量较小时 ToListByReflect 较快，还在测试中暂时无法保证性能准确性。
----


### 2.3 Dictionary
- `ToDictionary` 目前只支持[枚举]转[字典]

```csharp
enum MediaType { MP3 = 1,MP4,AVI,MOV,WMV,WAV }

var media = (new MediaType()).ToDictionary();
```
----


### 2.4 Enum
```csharp
public enum DateTimePart {
    /// <summary>
    /// 年
    /// </summary>
    [Description("年")]
    Year = 1,
    /// <summary>
    /// 月
    /// </summary>
    [Description("月")]
    Month,
    /// <summary>
    /// 日
    /// </summary>
    [Description("日")]
    Day,
    /// <summary>
    /// 小时
    /// </summary>
    [Description("小时")]
    Hour,
    /// <summary>
    /// 分钟
    /// </summary>
    [Description("分钟")]
    Minute,
    /// <summary>
    /// 秒
    /// </summary>
    [Description("秒")]
    Second
}

var lis = new DateTimePart().ToList();
var c = lis.Count();        // 6
var last = lis.Last();      // { Value = 5, Name = "Second", Description = "秒" }

var day = "Day".TryParse<DateTimePart>();   // DateTimePart.Day == day

var vas = new DateTimePart().GetValues();   // new[] { 1, 2, 3, 4, 5, 6 }

var nas = new DateTimePart().GetNames();    // new[] { "Year", "Month", "Day", "Hour", "Minute", "Second" }

var i = DateTimePart.Hour.GetIndex();       // 3

var s = DateTimePart.Hour.GetDescription(); // "小时"

```
----


### 2.5 Guid

```csharp
string str = "61835e0b-8d89-4d39-aa13-900e192ff824";
Guid gid = str.ToGuid();


Guid? v = null;
Guid gid = v.ToGuid(); // gid == Guid.Empty 即 gid == "00000000-0000-0000-0000-000000000000"

```
----


### 2.6 Number
  * `ToInt16`
  * `ToInt32`
  * `ToInt64`
  * `ToFloat`
  * `ToByte`
  * `ToDecimal`
  * `ToDouble`

```csharp
object obj = 1;
string str = "2";

var a1 = obj.ToInt16();
var a2 = str.ToInt16();

short? v = null;
v.ToInt16() // 0
v.ToInt16(-1) // -1


var b1 = obj.ToInt32();
var b2 =str.ToInt32();

int? v = null;
v.ToInt32() // 0
v.ToInt32(-1) // -1


var c1 = obj.ToInt64();
var c2 = str.ToInt64();

long? v = null;
v.ToInt64() // 0
v.ToInt64(-1) // -1


var d1 = obj.ToFloat();
var d2 = str.ToFloat();

float? v = null;
v.ToFloat() // 0
v.ToFloat(-1) // -1


var e1 = obj.ToByte();
var e2 = str.ToByte();

byte? v = null;
v.ToByte() // 0
v.ToByte(255) // 255


var f1 = obj.ToDecimal();
var f2 = str.ToDecimal();

decimal? v = null;
v.ToDecimal() // 0
v.ToDecimal(-1) // -1


var g1 = obj.ToDouble();
var g2 = str.ToDouble();

double? v = null;
v.ToDouble() // 0
v.ToDouble(-1) // -1

```
----


### 2.7 JSON
- `ToJson` `ToObject` 
  - 采用 `Newtonsoft.Json`
  - 支持自定义 `Newtonsoft.Json.JsonSerializerSettings`
  - `JsonNetExtensions.JsonNetSettings` 默认配置：
    - 启用输出忽略所有 null 值属性
    - 启用输出日期格式：`yyyy/MM/dd HH:mm:ss`
    - 启用采用蛇形命名策略，如："FooBar" ==> "foo_bar"，注：尽在不设置 JsonProperty 时有效
    - 启用压缩输出
- `ToJsonAsSystem` `ToObjectAsSystem`
  - 采用 `System.Text.Json`
  - 支持自定义 `System.Text.Json.JsonSerializerOptions`
  - `JsonMicrosoftExtensions.JsonOptions` 默认配置：
    - 启用输出忽略所有 null 值属性
    - 启用输出日期格式：`yyyy/MM/dd HH:mm:ss`
    - 启用采用蛇形命名策略，如："FooBar" ==> "foo_bar"，注：尽在不设置 JsonPropertyName 时有效
    - 启用压缩输出
    - 启用中文字符编码

```csharp
[JsonObject(MemberSerialization.OptIn)]
[Serializable]
class Movie {
    [System.Text.Json.Serialization.JsonPropertyName("nn")] // System.Text.Json 自定义 JSON 属性名
    [Newtonsoft.Json.JsonProperty("nn")]                    // Newtonsoft.Json 自定义 JSON 属性名
    public string Name { get; set; }

    [System.Text.Json.Serialization.JsonPropertyName("rd")]
    [Newtonsoft.Json.JsonProperty("rd"), Newtonsoft.Json.JsonConverter(typeof(JsonNetExtensions.DateTimeConverterExtensions))] // Newtonsoft.Json 自定义时间格式
    //[Newtonsoft.Json.JsonProperty("rd"), Newtonsoft.Json.JsonConverter(typeof(JsonNetExtensions.DateTimeConverterExtensions), "yyyy.MM.dd")]
    public DateTime ReleaseDate { get; set; }

    [System.Text.Json.Serialization.JsonPropertyName("g")]
    [Newtonsoft.Json.JsonProperty("g")]
    public string[] Genres { get; set; }

    [System.Text.Json.Serialization.JsonIgnore]     // System.Text.Json  忽略单个属性
    [Newtonsoft.Json.JsonIgnore]                    // Newtonsoft.Json  忽略单个属性
    public int Year { get; set; }

    [System.Text.Json.Serialization.JsonPropertyName("age")]
    [Newtonsoft.Json.JsonProperty("age")]
    public int? Age { get; set; }
}

var m = new Movie();
m.Name = "abc";
m.ReleaseDate = DateTime.Parse("2019-5-1");
m.Genres = new[] { "Action", "Comedy" };
m.Year = 2021;
m.Age = 18;

var str1 = m.ToJson(); 
// {"nn":"abc","rd":"2019/05/01 00:00:00","g":["Action","Comedy"],"age":18}

var str2 = m.ToJsonAsSystem();
// {"nn":"abc","rd":"2019/05/01 00:00:00","g":["Action","Comedy"],"age":18}

var m1 = str.ToObject<Movie>();

var m2 = str.ToObjectAsSystem<Movie>();

```

```csharp
class Movie2 {
    public string MovieName { get; set; }

    public DateTime ReleaseDate { get; set; }

    public string[] MovieGenres { get; set; }

    public string MovieVersion { get; set; }

    public int MovieYear { get; set; }

    public DateTime MovieTime { get; set; }
}


var m2 = new Movie2();
m2.MovieName = "abc";
m2.ReleaseDate = DateTime.Parse("2019-5-1");
m2.MovieGenres = new[] { "Action", "Comedy" };
m2.MovieVersion = "2021.03";
m2.MovieYear = 2021;
m2.MovieTime = DateTime.Parse("2021-03-15");


var t1 = m2.ToJson();
// {"movie_name":"abc","release_date":"2019/05/01 00:00:00","movie_genres":["Action","Comedy"],"movie_version":"2021.03","movie_year":2021,"movie_time":"2021/03/15 00:00:00"}
var mt1 = t1.ToObject<Movie2>();


var t2 = m2.ToJsonAsSystem();
// {"movie_name":"abc","release_date":"2019/05/01 00:00:00","movie_genres":["Action","Comedy"],"movie_version":"2021.03","movie_year":2021,"movie_time":"2021/03/15 00:00:00"}
var mt2 = t2.ToObjectAsSystem<Movie2>();
```



### 2.8 Stream
> **待完善**
----


### 2.9 String
支持以下类型转换成
- `object`
- `string`
- `DateTime` `DateTime?` ==> 默认格式 `yyyy/MM/dd HH:mm:ss`
- `bool` `bool?`
- `DataTable`
- `DataRow[]`
- `DataRow`
- `IEnumerable`

```csharp
DateTime? dt1 = null;
dt1.ToStrings()                         // null
dt1.ToStrings("yyyy/MM/dd")             // null
dt1.ToStrings("yyyy-MM-dd HH:mm:ss")    // null

DateTime dt1 = DateTime.Parse("2020-11-11");
dt1.ToStrings()                         // "2019/05/01 00:00:00"
dt1.ToStrings("yyyy/MM/dd")             // "2019/05/01"
dt1.ToStrings("yyyy-MM-dd HH:mm:ss")    // "2019-05-01 00:00:00"



bool? b1 = true;
b1.ToStrings()  // true

b1 = false;
b1.ToStrings()  // false

b1 = null;
b1.ToStrings()  // false

string str = "  xxxx   ";
str.ToStrings() // "xxxx" 默认去除收尾空格



var dt = new DataTable();
dt.Columns.Add("PackageId", typeof(string));
dt.Columns.Add("Version", typeof(string));
dt.Columns.Add("ReleaseDate", typeof(DateTime));
dt.Rows.Add("Newtonsoft.Json", "11.0.1", new DateTime(2018, 2, 17));
dt.Rows.Add("Newtonsoft.Json", "10.0.3", new DateTime(2017, 6, 18));

dt.ToStrings(0, 1)                  // "11.0.1"
dt.ToStrings(0, "Version")          // "11.0.1"

foreach (DataRow item in dt.Rows)
{
    item.ToStrings(0)               // "Newtonsoft.Json"
    item.ToStrings("PackageId")     // "Newtonsoft.Json"
}

DataRow[] rows = dt.Select();
rows.ToStrings(1, 2)                // "2017/6/18 0:00:00"
rows.ToStrings(1, "ReleaseDate")    // "2017/6/18 0:00:00"



var arr = new[] { 1, 2, 3, 4, 5 };
arr.ToStrings(",")      // "1,2,3,4,5"

var ls = arr.Where(x => x % 2 == 0);
ls.ToStrings("|");      // 2|4

```
----


### 2.10 Time
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

```csharp
object o = DateTime.Parse("2019/5/1");
o.ToDateTime();                         // 2019/5/1 00:00:00
o.ToDateTime("yyyy-MM-dd HH:mm:ss");    // 2019-5-1 00:00:00
"2019/5/1".ToDateTime();                // 2019/5/1 00:00:00


// Unix 时间戳 ==> DateTime
1556687655000.ToDateTime();                             // 2019/5/1 13:14:15
DateTime.Parse("2019/5/1 13:14:15").ToUnixTimestamp();  // 1556687655000


// Excel 日期转换，如 Excel 日期 43799.9820949074 ==> 2019/11/30 23:34:13，失败时返回 null
double? dou = 43799.9820949074;
DateTime? dts = dou.ToDateTime();   // 2019/11/30 23:34:13

dou = null;
dts = dou.ToDateTime();             // null


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
----


### 2.11 Words
- `ToChineseDate` 转换为农历年
- `ToChineseDay` 数字转换中文日期数字 (支持 1~31)
- `ToChineseMonth` 数字转换成中文月份数字

以下内容请移步 `ToolGood.Words` 官网查看详情 **<https://github.com/toolgood/ToolGood.Words>**
- `ToSBC` 半角转全角
- `ToDBC` 全角转半角
- `ToChineseRMB` 数字转成中文大写
- `ToNumber` 大写中文数字转数字
- `ToSimplifiedChinese` 繁体转简体
- `ToTraditionalChinese` 简体转繁体
- `HasChinese` 是否含有中文
- `IsAllChinese` 是否全为中文（不能有任何符号与空格）
- `HasEnglish` 是否含有英语
- `IsAllEnglish` 是否全为英语（不能有任何符号与空格）
- `GetFirstPinYin` 获取首字母
- `GetPinYin` 获取拼音全拼

```csharp
DateTime.Now.ToChineseDate() // "庚子年十月廿六"

(1).ToChineseDay();     // "一"
(11).ToChineseDay();    // "十一"
(0).ToChineseDay();     // null
(33).ToChineseDay();    // null

(1).ToChineseMonth();   // "一月"
(11).ToChineseMonth();  // "十一月"
(0).ToChineseMonth();   // null
(33).ToChineseMonth();  // null


"abcABC123".ToSBC()         // "ａｂｃＡＢＣ１２３"
"ａｂｃＡＢＣ１２３".ToDBC()    // "abcABC123"

(123.45).ToChineseRMB()         // "壹佰贰拾叁元肆角伍分"
"壹佰贰拾叁元肆角伍分".ToNumber()   // 123.45


"简体转换繁体".ToTraditionalChinese() // "簡體轉換繁體"
"簡體轉換繁體".ToSimplifiedChinese()  // "简体转换繁体"

var chs = "GO语言算法优化：在GO版本移植成功后，测试后性能不理想，性能 GO < JAVA < C#，因为我没有完全理解GO语言。JAVA与C#采用了新的算法，GO还是采用老的算法。";
chs.HasChinese();       // true
chs.IsAllChinese();     // false
chs.HasEnglish();       // true
chs.IsAllEnglish();     // false

chs = "细分敏感词分类涉政文本涉爆文本色情文本辱骂文本非法交易文本广告导流文本";
chs.HasChinese();       // true
chs.IsAllChinese();     // true

chs = "JsonNETisapopularhighperformanceJSONframeworkforNET";
chs.HasEnglish();       // true
chs.IsAllEnglish();     // true

chs = "我骑着自行车去的中国人民银行";
chs.GetFirstPinYin();   // "WQZXCQZGRMYH"
chs.GetPinYin();        // "WoQiZiXingCheQuZhongGuoRenMinYinHang"
chs.GetPinYinAsSpace(); // "Wo Qi Zi Hang Che Qu Zhong Guo Ren Min Yin Hang"
chs[3].GetAllPinYin();  // new[] { "Hang", "Xing", "Heng" }

```
----


## 3.Linq 扩展
- [1.Add](#31-add)
- [2.Distinct](#32-distinct)
- [3.ForEach](#33-foreach)
- [4.IndexOf](#34-indexof)
- [5.Insert](#35-insert)
----

### 3.1 Add
- 扩展方法内部避免了 `null` 引发异常

```csharp
IEnumerable<string> q1 = null;
IEnumerable<string> v1 = q1.Add("10"); // new[] { "10" }


IEnumerable<string> q2 = null;
string s1 = null;
IEnumerable<string> v2 = q2.Add(s1); // new string[0]，空集合并非 null


List<int> list = null;
IEnumerable<int> v3 = list.Add(11, 22, 33); // new[] { 11, 22, 33 }

IEnumerable<int> list2 = new List<int> { 1, 2, 3 };
IEnumerable<int> v4 = list2
                        .Add(10)
                        .Add(11, 12)
                        .Add(new[] { 13, 14 })
                        .Add(v3);
// new[] { 1, 2, 3, 10, 11, 12, 13, 14, 11, 22, 33 }

```
---


### 3.2 Distinct
- 增加了对 `委托` 的支持

```csharp
var lis = new List<Movie>
{
    new Movie {Name = "钢铁侠", Year = 2008},
    new Movie {Name = "无敌浩克", Year = 2008},
    new Movie {Name = "钢铁侠2", Year = 2010},
    new Movie {Name = "雷神", Year = 2011},
    new Movie {Name = "美国队长：复仇者先锋", Year = 2011},
    new Movie {Name = "复仇者联盟", Year = 2012},

    new Movie {Name = "钢铁侠", Year = 2008},
    new Movie {Name = "钢铁侠2", Year = 2010},
};

var year = lis
    .Distinct(x => new { x.Year })
    .Select(x => x.Year);
// new[] { 2008, 2010, 2011, 2012 }


var name = lis
    .Distinct(x => new { x.Name })
    .Select(x => x.Name);
// new[] { "钢铁侠", "无敌浩克", "钢铁侠2", "雷神", "美国队长：复仇者先锋", "复仇者联盟" }


var mo = lis
    .Distinct(x => new { x.Name, x.Year })
    .Select(x => x);
/*
 new Movie[]
 {
     new Movie {Name = "钢铁侠", Year = 2008},
     new Movie {Name = "无敌浩克", Year = 2008},
     new Movie {Name = "钢铁侠2", Year = 2010},
     new Movie {Name = "雷神", Year = 2011},
     new Movie {Name = "美国队长：复仇者先锋", Year = 2011},
     new Movie {Name = "复仇者联盟", Year = 2012}
 }
*/


```
---


### 3.3 ForEach
- 增加了对 `IEnumerable<T>` 的支持

```csharp
IEnumerable<int> lis1 = new[] { 1, 2, 3 };

lis1.ForEach(value => {
    Console.WriteLine(value);
});
// 1 2 3


lis1.ForEach((value, index) => {
    Console.WriteLine($"value: {value}, index: {index}");
});
// value: 1, index: 0
// value: 2, index: 1
// value: 3, index: 2
```

---


### 3.4 IndexOf
- 增加了对 `IEnumerable<T>` 的支持

```csharp
IEnumerable<int> list = new[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

list.IndexOf(1);    // 0
list.IndexOf(3);    // 2
list.IndexOf(7);    // 6
list.IndexOf(9);    // 8

```
---


### 3.5 Insert
- 将新序列插入到原序列的指定索引处
- `原序列`与`新序列`不能为 `null`
- `index` 索引不能小于 `0`
- `index` 索引大于原序列长度时，采用原序列长度代替 `index`

```csharp
var seq0 = new[] { 97, 98, 99 };
var seq1 = new[] { 0, 1, 2 };
var seq2 = new[] { 0, 1, 2, 3, 4 };
var seq3 = new[] { 0, 1, 2, 3, 4, 5, 6 };

// 添加到最后
seq1.Insert(seq0);      // new[] { 0, 1, 2, 97, 98, 99 }

// 添加到指定索引位置
seq2.Insert(seq0, 0)    // new[] { 97, 98, 99, 0, 1, 2, 3, 4 }
seq2.Insert(seq0, 1)    // new[] { 0, 97, 98, 99, 1, 2, 3, 4 }
seq2.Insert(seq0, 2)    // new[] { 0, 1, 97, 98, 99, 2, 3, 4 }


```
---


## 4.加密
- [1.Base64](#41-base64)
- [2.DESEncrypt](#42-desencrypt)
- [3.MD5](#43-md5)
- [4.SHA1](#44-sha1)


### 4.1 Base64

```csharp
// 默认编码 utf-8
var b64 = val.Base64Encode();
var str = b64.Base64Decode();

// 自定义编码
var b64 = val.Base64Encode(Encoding.Default);
var str = b64.Base64Decode(Encoding.Default);
```


### 4.2 DESEncrypt

*[appsettings.json]*
```JSON
{
    "AppSettings": {
        "DESKey": "DES-keys"
    }
}
```

*[web.config]*
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


### 4.3 MD5

```csharp
var md5 = str.MD5();
```


### 4.4 SHA1

```csharp
var sha1 = str.SHA1();
```


## 5.基于系统方法的扩展
- [1.Append](#51-append)
- [2.Joins](#52-joins)
- [3.Lengths](#53-lengths)
- [4.MapPaths](#54-mappaths)
- [5.Replaces](#55-replaces)
- [6.Splits](#56-splits)
- [7.Substrings](#57-substrings)
- [8.ToTitleCase](#58-totitlecase)
- [9.Trims](#59-trims)
- [10.StringCut](#510-stringcut)


### 5.1 Append

```csharp
string s1 = "string";
object o1 = "object";
int i1 = 123;
char c1 = '-';
string c2 = "-";

var val = s1
    .Append(c1)
    .Append(o1)
    .Append(c2)
    .Append(i1);                    // "string-object-123"

var v2 = val.AppendBefore("@@");    // "@@string-object-123"

```


### 5.2 Joins

```csharp
var list = new List<string>();
list.Joins("_");    // string.Empty

list = null;
list.Joins("_");    // null


list = new List<string>();
list.Add("A");
list.Add("B");
list.Add("C");
list.Joins("_");    // "A_B_C"
```


### 5.3 Lengths

```csharp
var str = "获取当前System.String";

var i1 = str.Lengths(); // 21
var i2 = str.Length;    // 17
```


### 5.4 MapPaths
- 文件存在返回绝对路径，文件不存在返回 `null`
- 网址路径，则直接返回网址本身，支持 `http://` `https://`

```csharp
var v0 = "https://github.com/xxx/.../master/appsettings.json".MapPaths();   // 网址本身

var v2 = "\\App_Data\\log4net.config".MapPaths();       // C:\IIS\App_Data\log4net.config
var v3 = "App_Data/log4net.config".MapPaths();          // C:\IIS\App_Data\log4net.config

```


### 5.5 Replaces

```csharp
var str = "获取当前System.String";

str.Replaces("S");                          // "获取当前ystem.tring"
str.Replaces("S", "A");                     // "获取当前Aystem.Atring"
str.Replaces(new[] { "S", "获取" }, "@");    // "@当前@ystem.@tring"
```


### 5.6 Splits
- 返回值不包括包含空字符串的数组元素
- 数组每项会删除开头和结尾空白字符
- 返回序列的每个元素均已去除收尾空格

```csharp
var str = "获取当前System.String对象中的字符数";
str.Splits(".", "的", "当");     // new[] { "获取", "前System", "String对象中", "字符数" }
str.Splits(null);               // new[] { "获取当前System.String对象中的字符数" }
str.Splits(new string[] { });   // new[] { "获取当前System.String对象中的字符数" }


str.Splits("S");                // new[] { "获取当前", "ystem.", "tring对象中的字符数" }
str.Splits('S');                // new[] { "获取当前", "ystem.", "tring对象中的字符数" }


var s1 = "/Sys //tem/ / .String ";
s1.Split('/');                  // 系统方法  new[] { "", "Sys ", "", "tem", " ", " .String " }
s1.Splits("/");                 //         new[] { "Sys", "tem", "", ".String" }



string str = null;
str.Splits(".");      // null
str.SplitsOrDefault('.'); //返回一个空集合 new string[0]
str.SplitsOrDefault("|"); //返回一个空集合 new string[0]
```


### 5.7 Substrings
- 截取字符串，区分中英文字符长度，中文按1:2，英文按1:1 计算长度

```csharp
var str = "获取当前System.String对象中的字符数";

str.Substrings(7);          // "获取当"
str.Substrings(11);         // "获取当前Sys"
str.Substrings(11, "___");  // "获取当前Sys___"
str.Substring(0, 11);       // "获取当前System."

```


### 5.8 ToTitleCase
- 将指定字符串转换为词首字母大写

```csharp
"codec_long_name".ToTitleCase();    // "Codec_Long_Name"
"codeclongname".ToTitleCase();      // "Codeclongname"
"codec long name".ToTitleCase();    // "Codec Long Name"
"一个.net core平台".ToTitleCase();   // "一个.Net Core平台"



string[] values =
{
    "a tale of two cities",
    "gROWL to the rescue",
    "inside the US government",
    "sports and MLB baseball",
    "The Return OF Sherlock Holmes",
    "UNICEF and children"
};
foreach (var item in values) {
    Console.WriteLine("{0} --> {1}", item, item.ToTitleCase());
}

// a tale of two cities --> A Tale Of Two Cities
// gROWL to the rescue --> Growl To The Rescue
// inside the US government --> Inside The US Government
// sports and MLB baseball --> Sports And MLB Baseball
// The Return OF Sherlock Holmes --> The Return OF Sherlock Holmes
// UNICEF and children --> UNICEF And Children

```


### 5.9 Trims

```csharp
var val = str.Trims();                    // 等同于 str?.Trim();

var str = "获取当前System.String对象中的字符数";
str.Trims('获', '数');          // "取当前System.String对象中的字符"
str.Trims(null);               // "获取当前System.String对象中的字符数"
```


### 5.10 StringCut
- 字符串裁剪


```csharp
var str = "Newtonsoft.Json";
str.StringCut("New", ".");      // tonsoft
```


## 6.验证与判断
- [1.IsNull](#61-isnull)
- [2.IsRange](#62-isrange)

### 6.1 IsNull
- 判断是否为 null or empty，简化 string.IsNullOrWhiteSpace 方便调用
- 支持已下类型：
  - `string`
  - `object`
  - `DBNull`
  - `DataTable`
  - `DataRowCollection`
  - `DataRow`
  - `DataRow[]`
  - `DataSet`
  - `IEnumerable`


```csharp
object o = null;
o.IsNull()				// true

string str = null;
str.IsNull()				// true
"".IsNull()				// true
" ".IsNull()				// true

var o = DBNull.Value;
o.IsNull()				// true



DataTable table = null;
table.IsNull()				// true

table = new DataTable();
table.IsNull()				// true
table.Rows.IsNull()			// true

table.Rows.Add()
table.Rows.Add()
table.IsNull()				// false
table.Rows.IsNull()			// false


DataSet ds = null;
ds.IsNull()				// true

ds = new DataSet();
ds.IsNull()				// true

ds.Tables.Add(new DataTable());
ds.Tables.Add(new DataTable());
ds.IsNull();				// false


var list = new List<string>();
list.IsNull();				// true
list = null;
list.IsNull();				// true
```


### 1.2 IsRange
- 支持检测 数值/时间/集合 是否在的范围内


```csharp
var a = 1;
var b = 10;
var c = 5;
var d = 14;
c.IsRange(a, b);		// true
d.IsRange(a, b);		// false


var a = DateTime.Parse("2019/5/1");
var b = DateTime.Parse("2019/5/5");
var c = DateTime.Parse("2019/5/2");
var d = DateTime.Parse("2019/5/6");
c.IsRange(a, b);		// true
d.IsRange(a, b);		// false


var list = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
var val1 = 3;
var val2 = 55;
val1.IsRange(list);		// true
val2.IsRange(list);		// false
```


## 7.其他
- [1.GetIPv4](#71-getipv4)
- [2.GetUnixTimestamp](#72-getunixtimestamp) 获取时间戳（毫秒），13位
- [3.ConvertBytes](#73-convertbytes) 单位转换 Bytes,KB,MB,GB,TB
- [4.Random](#74-random)


### 7.1 GetIPv4


```csharp
string[] ip = UtilsBasis.GetIPv4;	// new[] { "192.168.132.1", "192.168.126.1", "192.168.3.100" }
```


### 7.2 GetUnixTimestamp
- 获取时间戳（毫秒），13位


```csharp
var t = UtilsBasis.GetUnixTimestamp;	// 1607875432593
```

### 7.3 ConvertBytes
- 单位转换 Bytes, KB, MB, GB, TB


```csharp
UtilsBasis.ConvertBytes(245134695);	// "233.78 MB"
UtilsBasis.ConvertBytes(1592494871);	// "1.48 GB"
UtilsBasis.ConvertBytes(73181050853);	// "68.16 GB"
UtilsBasis.ConvertBytes(316969);	// "309.54 KB"
```


### 7.4 Random
- RandomNumber: 随机纯数字
- RandomString: 随机数字+字母
- RandomStringByPattern: 随机支持自定义生成样式，默认：##??**
  - "?"代表一个字符
  - "#"代表一个一位数字
  - "*"代表一个字符串或一个一位数字


```csharp
UtilsBasis.RandomNumber()	// "707785"
UtilsBasis.RandomNumber(10)	// "6102154083"


UtilsBasis.RandomString()	// "20NB66"
UtilsBasis.RandomString(10)	// "DX6ZVVNRPF"


UtilsBasis.RandomStringByPattern()		// "36hBLo"
UtilsBasis.RandomStringByPattern("####-???")	// "5065-CEc"

```


