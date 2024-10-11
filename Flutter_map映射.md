# Flutter_map映射

1. **定义映射**:
   
   ```dart
   Map<int, String> batteryImages = {
    100: 'assets/imgs/battery-100.png',
    80: 'assets/imgs/battery-80.png',
    60: 'assets/imgs/battery-60.png',
    40: 'assets/imgs/battery-40.png',
    20: 'assets/imgs/battery-20.png',
    0: 'assets/imgs/battery-0.png',
   };
   ```
   
   这段代码定义了一个名为`batteryImages`的映射，其中键（key）是电池的电量（以百分比表示），值（value）是相应的图片路径。这个映射包含了从100%到0%的电量，每个电量值都对应一个图片路径。

2. **根据电量选择图片**:
   
   ```dart
   String batteryImage = batteryImages.entries
      .firstWhere((entry) => battery_status >= entry.key,
          orElse: () => MapEntry(0, 'assets/battery/battery-0.png'))
      .value;
   ```
   
   这段代码使用了`batteryImages`映射来找到最接近但不低于当前电池状态（battery_status）的图片路径。它首先通过`.entries`方法获取映射的所有项，然后使用`.firstWhere`方法找到第一个满足条件的项。这里的条件是`battery_status >= entry.key`，这意味着它将找到第一个电量值大于或等于当前电池状态的项。
   
   如果`battery_status`的值不在映射的电量范围内（例如，如果它小于0%或大于100%），`firstWhere`方法将返回`null`。为了避免这种情况，代码中使用了`.orElse`方法，它提供了一个默认值，即当`firstWhere`没有找到匹配项时返回的值。这个默认值是一个`MapEntry`，其中键是0（代表无电或电量极低），值是`'assets/battery/battery-0.png'`，即无电状态下的图片路径。
   
   最后，`.firstWhere`方法返回的是一个包含键和值的`MapEntry`对象，我们通过`.value`属性获取了图片路径，并将其赋值给`batteryImage`变量。

总结：这段代码定义了一个映射，用于根据电池状态选择相应的图片路径，并在电池状态不在预定义范围内时提供了一个默认的图片路径。



在Dart中，当使用`firstWhere`方法时，需要提供一个<mark>Lambda表达式</mark>作为条件。这个Lambda表达式接收一个`MapEntry`对象作为参数，其中包含键和值。在这个Lambda表达式中，你使用`.key`属性来获取键（在这个情况下是电量值），并使用`.value`属性来获取值（在这个情况下是图片路径）。

`.firstWhere`方法需要一个返回布尔值的Lambda表达式。在这个表达式中，`battery_status >= entry.key`是一个条件表达式，它检查当前的`battery_status`是否大于或等于`entry.key`。由于这个条件表达式的结果是一个布尔值（true或false），所以它符合`.firstWhere`方法的要求。
