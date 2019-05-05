# Rex.Utils

## 简介
基于框架 `.NET Framework 4.7`，包含一系列快速开发中经常用到的 Utility 辅助功能。

### 1. 验证判断
- IsNull

  判断是否为空

- IsInRange

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

------------

### 2. 基础加密
- Base64

*[C#]*

```csharp
// 默认编码 utf-8
var b64 = val.Base64Encode();
var str = b64.Base64Decode();

// 自定义编码
var b64 = val.Base64Encode(Encoding.Default);
var str = b64.Base64Decode(Encoding.Default);
```

- DESEncrypt

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

- MD5

*[C#]*

```csharp
val.MD5();
```

- SHA1

*[C#]*

```csharp
val.SHA1();
```

------------

### 3. 类型转换
- ToBool
- ToDictionary
- ToList
- ToGuid

* **Number**
  * ToInt16
  * ToInt32
  * ToInt64
  * ToFloat
  * ToByte
  * ToDecimal
  * ToDouble

- **Time**
  * ToDateTime
  * ToUnixTimestamp
  * GetTimeFormat
  * GetTime

* **JSON**
  * ToJson
  * ToDeserializeObject
  * JsonNetSerializerSettings
  * DateFormat
  * DateTimeFormat

- **基于系统扩展**
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

------------

### 4. 文字处理
> for ToolGood.Words

#### 4.1. 半角 ==> 转换 <== 全角
- ToSBC
- ToDBC

*[C#]*

```csharp
str.ToSBC(); // abcABC123 ==> ａｂｃＡＢＣ１２３
str.ToDBC(); // ａｂｃＡＢＣ１２３ ==> abcABC123
```

#### 4.2. 数字 ==> 转换 <== 中文大写
- ToChineseRMB
- ToNumber

*[C#]*

```csharp
double val = 123.45;
val.ToChineseRMB(); // 123.45 ==> 壹佰贰拾叁元肆角伍分
str.ToNumber();     // 壹佰贰拾叁元肆角伍分 ==> 123.45
```

#### 4.3. 简体 ==> 转换 <== 繁体
- ToSimplifiedChinese
- ToTraditionalChinese

*[C#]*

```csharp
str.ToTraditionalChinese(); // 简体转换繁体 ==> 簡體轉換繁體
str.ToSimplifiedChinese();  // 簡體轉換繁體 ==> 简体转换繁体
```

#### 4.4. 判断输入是否为中文/英文
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

#### 4.5. 拼音
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

------------

### 5. 记录日志 LogHelper
> for log4net

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

------------

### 6. 配置文件读取 AppSettings
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
