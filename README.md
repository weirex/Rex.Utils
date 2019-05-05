# Rex.Utils

## 简介
基于框架 `.NET Framework 4.7`，包含一系列快速开发中经常用到的 Utility 辅助功能。

#### 1. 验证判断
- IsNull
- IsInRange

#### 2. 加密方法
- Base64
- DESEncrypt
- MD5
- SHA1

#### 3. 类型转换
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

#### 4. 文字处理
> for ToolGood.Words

 * 半角 ==> 转换 <== 全角
   * ToSBC
   * ToDBC
 * 数字 ==> 转换 <== 中文大写
   * ToChineseRMB
   * ToNumber
 * 简体 ==> 转换 <== 繁体
   * ToSimplifiedChinese
   * ToTraditionalChinese
 * 判断输入是否为中文/英文
   * HasChinese
   * IsAllChinese
   * HasEnglish
   * IsAllEnglish
 * 拼音
   * GetFirstPinYin
   * GetPinYin
   * GetPinYinSpace
   * GetAllPinYin

#### 5. 记录日志 LogHelper
> for log4net

- Error
- Fatal
- Warn
- Info
- Debug

#### 6. 配置文件读取 AppSettings
- GetValue
