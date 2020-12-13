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


#### 5.1 Append

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


#### 5.2 Joins

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


#### 5.3 Lengths

```csharp
var str = "获取当前System.String";

var i1 = str.Lengths(); // 21
var i2 = str.Length;    // 17
```


#### 5.4 MapPaths
- 文件存在返回绝对路径，文件不存在返回 `null`
- 网址路径，则直接返回网址本身，支持 `http://` `https://`

```csharp
var v0 = "https://github.com/xxx/.../master/appsettings.json".MapPaths();   // 网址本身

var v2 = "\\App_Data\\log4net.config".MapPaths();       // C:\IIS\App_Data\log4net.config
var v3 = "App_Data/log4net.config".MapPaths();          // C:\IIS\App_Data\log4net.config

```


#### 5.5 Replaces

```csharp
var str = "获取当前System.String";

str.Replaces("S");                          // "获取当前ystem.tring"
str.Replaces("S", "A");                     // "获取当前Aystem.Atring"
str.Replaces(new[] { "S", "获取" }, "@");    // "@当前@ystem.@tring"
```


#### 5.6 Splits
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


#### 5.7 Substrings
- 截取字符串，区分中英文字符长度，中文按1:2，英文按1:1 计算长度

```csharp
var str = "获取当前System.String对象中的字符数";

str.Substrings(7);          // "获取当"
str.Substrings(11);         // "获取当前Sys"
str.Substrings(11, "___");  // "获取当前Sys___"
str.Substring(0, 11);       // "获取当前System."

```


#### 5.8 ToTitleCase
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


#### 5.9 Trims

```csharp
var val = str.Trims();                    // 等同于 str?.Trim();

var str = "获取当前System.String对象中的字符数";
str.Trims('获', '数');          // "取当前System.String对象中的字符"
str.Trims(null);               // "获取当前System.String对象中的字符数"
```


#### 5.10 StringCut
- 字符串裁剪


```csharp
var str = "Newtonsoft.Json";
str.StringCut("New", ".");      // tonsoft
```
