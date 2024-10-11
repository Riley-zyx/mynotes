# flutter_时间处理

## step1：导入包

```dart
import 'package:intl/intl.dart'; // 引入第三方库，用于 DateFormat 类
import 'dart:core'; // 用于 DateTime 类
```

`import 'package:intl/intl.dart';` 引入的是第三方库。`intl` 是一个常用的 Dart 包，它提供了国际化的支持，包括日期和数字的格式化。

需要在项目的 `pubspec.yaml` 文件中添加以下依赖

```yaml
dependencies:
  intl: ^0.17.0 # 使用适当的版本号
```

## Step2: 用法

### 常用1

```dart
DateFormat('dd MMM yyyy HH:mm')
     .format(DateTime.fromMillisecondsSinceEpoch(
     listData['datetime'] * 1000))
     .toString()
     .substring(0, 17)
```

1. **创建日期格式对象**：
   
   - `DateFormat('dd MMM yyyy HH:mm')` 创建了一个 `DateFormat` 对象，该对象用于定义日期时间的格式。这里的格式是：
     - `dd` 表示两位数的日
     - `MMM` 表示<mark>月份的缩写</mark>（例如，Jan、Feb、Mar 等）
     - `yyyy` 表示四位数的年份
     - `HH:mm` 表示两位数的小时和分钟

2. **从毫秒时间戳创建 `DateTime` 对象**：
   
   - `DateTime.fromMillisecondsSinceEpoch(listData['datetime'] * 1000)` 根据 Unix 时间戳（自1970年1月1日以来的毫秒数）创建一个 `DateTime` 对象。这里的 `listData['datetime']` 应该是一个Unix时间戳（通常为秒数），乘以1000将其转换为毫秒。

3. **格式化 `DateTime` 对象**：
   
   - `.format()` 方法使用前面创建的 `DateFormat` 对象来格式化 `DateTime` 对象。

4. **转换为字符串**：
   
   - `.toString()` 方法将格式化后的日期时间转换为字符串。

5. **截取字符串**：
   
   - `.substring(0, 17)` 方法截取字符串的前17个字符。这是因为在格式 `'dd MMM yyyy HH:mm'` 下，日期时间字符串的前17个字符是完整的日期时间表示（包括小时和分钟），但这里可能有额外的字符（如秒或时区信息），所以通过截取前17个字符来确保字符串的格式正确。

### 常用2

```dart
DateFormat formatter = DateFormat('yyyy-MM-dd HH:mm:ss');
```

这里的 `'yyyy-MM-dd HH:mm:ss'` 是一个格式字符串，其中：

- `yyyy` 表示四位数的年份
- `MM` 表示两位数的月份
- `dd` 表示两位数的日期
- `HH` 表示两位数的小时（24小时制）
- `mm` 表示两位数的分钟
- `ss` 表示两位数的秒
