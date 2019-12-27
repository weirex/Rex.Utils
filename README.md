# Rex.Utils.Core

## 简介
框架 `.NET Standard 2.1` `.NET Framework 4.7`，包括开发中常用的扩展方法。

## 目录
* [更新日志](CHANGELOG.md "更新日志")（2019.12.25）
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
  * `GetF
