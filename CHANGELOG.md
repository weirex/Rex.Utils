# Change log


## 5.0.6 （2021.06.11）
- 移除 `ResultModel` 对象
- 增加 `ToJsonNS` `ToObjectNS` `ToJsonAsSystemNS` `ToObjectAsSystemNS` 方法，不设置任何序列化、反序列化配置项；
- 扩展 `System.IO`
  - `str.ReplaceInvalidFileName()` 将不允许在文件名中使用的字符替换成下划线；
  - `str.GetFileNameWithoutExtension()` 原始方法 `Path.GetFileNameWithoutExtension` 扩展，增加为空判断，避免异常；
  - `str.GetFileName()` 原始方法 `Path.GetFileName` 扩展，自动判断是否为空，避免异常；
  - `str.GetExtension()`  原始方法 `Path.GetExtension` 扩展，自动判断是否为空，避免异常；
  - `IOExt.DeleteFile()` 删除文件，自动判断源文件是否存在；
  - `IOExt.MoveFile()` 移动文件，自动判断源文件是否存在；
  - `IOExt.Reader()` 读取文本内容，源文件不存在则返回null
  - `IOExt.Writer()` 写入文本内容，自动创建目录结构；
  - `IOExt.Downloader()` 下载文件（简易版）大于5MB不建议使用；
  - `IOExt.CreateDirectory()` 创建目录，自动判断目录是否存在；
  - `IOExt.DeleteDirectory()` 删除目录，自动判断目录是否存在；
  - `IOExt.MoveDirectory` 将文件或目录及其内容移到新位置，自动判断目录是否存在；
  - `IOExt.GetFiles()` 原始方法 `Directory.GetFiles` 扩展，返回指定目录中文件的名称；
  - `IOExt.GetDirectories()` 原始方法 `Directory.GetDirectories` 扩展，返回指定目录中文件的名称；


## 5.0.5.3 （2021.03.24）
- `Rex.Utils.Core` 更新包版本
- `Rex.Microsoft.AspNetCore.Mvc.Extensions` 更新包版本
- `Rex.Helper.Core` 更新包版本


## 5.0.5 （2021.03.15）
- 调整 `ToJson()` 扩展方法，实体属性不设置 `JsonProperty` 时，默认采用 `SnakeCaseNamingStrategy` 规则，如：FooBar =输出=> foo_bar
- 调整 `ToJsonAsSystem()` 扩展方法，实体属性不设置 `JsonPropertyName` 时，默认采用 `SnakeCaseNamingStrategy` 规则，如：FooBar =输出=> foo_bar
- `ToJson()` 和 `ToJsonAsSystem()` 不再捕获异常，触发异常时直接抛出
- 优化部分代码


## 3.2.3 （2020.06.01）
- 增加 UtilsBasis.GetUnixTimestamp 返回毫秒时间戳（e.g. 1591115598575）
- 调整部分代码

## 3.1
- 增加 `AppSettings.GetValueToDirectory` 扩展方法


## 3.0
- 增加 枚举 ToList 方法，例如：`var list = new Color().ToList();`
- 扩展 `ResultModel`


## 2.2.1.5 （2020.05.01）
- 调整 `new ResultModel(int,string,object)` 返回 json `{ code:0,msg:"ok",data:{} }`
- 增加 `new ResultInfo(int,string,object)` 返回 json `{ code:0,msg:"ok",result:{} }`
- 增加 `new ResultInfo<T>(int,string,T)` 返回 json `{ code:0,msg:"ok",result:{} }`
- 增加 `ResultModel.Success()` `ResultModel.Error()`
- 增加 `ResultInfo.Success()` `ResultInfo.Error()`
- 增加 `ResultInfo<T>.Success()` `ResultInfo<T>.Error()`


## 2.2.1.4 （2020.04.22）
- 增加 `SplitsDefault` 扩展方法，当拆分字符串是 null 时返回一个默认集合
- 增加 `ResultModel<T>`，扩展 `ResultModel.Success(), ResultModel<T>.Success(), ResultModel.Error(), ResultModel<T>.Error()`


## 2.2.1 （2020.03.25）
- 增加 `IEnumerable<T>` 扩展 Add
- 优化部分代码


## 2.2.0 （2019.12.25）
- 调整部分代码的依赖关系
- 增加 针对 `IEnumerable` 类型的 `IndexOf` 扩展
- 增加 单例类助手 `Singleton` 例：`public class ClassA : Singleton<ClassA>`
- 增加 `Distinct` 扩展更符合 Linq 一贯的风格 `var p1 = products.Distinct(p => p.ID);`
- 修改 `ResultModel` 命名空间 `System.GeneralExtensions.ResultModel`【和自己的其他项目有冲突了。。。】


## 2.1.0.3 （2019.11.09）
- 支持 `.NET Standard 2.1` `.NET Framework 4.7`
- 重写部分代码
- 优化部分代码
- 增加 `ToDataTable<T>`
- 增加 `ToDateTimes` 支持类型 `DateTime?`，可将Excel日期转换（43799.9820949074 ==> 2019/11/30 23:34:13）
- 增加 `.Distinct()` 扩展 ==> `.Distinct(x=> x.Name)`


## 1.0.8 （2019.07.30）
- 增加：
    - 实体：System.Extend.Models.ResultModel

```csharp
var result = new ResultModel { Code = 1000, Msg = "成功", Data = new { id = 1, name = "xx" } };
var json = paramsErrorResult.ToJson();
```

输出 JSON

```javascript
{
    "code": 1000,
    "msg": "成功",
    "data": {
        "id": 1,
        "name": "xx"
    }
}
```



## 1.0.7 （2019.07.21）
- Bug修复和小的调整



## 1.0.6 （2019.07.20）
#### 添加：
##### 1. 时间相关：
- `ToChineseDate` 转换为农历年
- `ToChineseDay` 阿拉伯数字转换中文日期数字
- `ToChineseMonth` 阿拉伯数字转换成中文月份数字
- `ToStrings<T>`
- `EndOfDay`  一天末尾时间
- `EndOfMonth`  一个月末尾时间
- `EndOfWeek`  一周末尾时间
- `EndOfYear` 一年末尾时间
- `FirstDayOfWeek` 一周的第一天
- `GetAge` 根据出生日期计算年龄
- `GetDateDiff` 日期差计算
- `GetDays` 获取一个月有多少天
- `GetFriendlyString` 友好时间
- `GetWeekdays` 返回两个时间内工作日天数
- `GetWeekends` 返回两个时间内周末天数
- `GetWeekNumber` 获取日期是一年中第几个星期
- `IsToday` 日期是否是今天
- `IsWeekDay` 是否是工作日
- `IsWeekEnd` 是否是周末

##### 2. 字典相关：
- `AddOrReplace` 添加或替换内容
- `AddRange` 合并两个字典
- `GetValue` 字典取值

#### 调整：
- ` LogHelper.Fatal(MethodBase.GetCurrentMethod(), ex);` ==> `LogHelper.Debug(this, ex);`



## 1.0.5
- Bug修复和小的调整
- 更名：`IsInRange` ==> `IsRange`
