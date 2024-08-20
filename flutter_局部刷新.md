# 局部刷新的多种用法

## 第一类：不用第三方库

### StatefulBuilder

```dart
// step1
late StateSetter wpsStatesetter;         // build方法外定义

// step2
StatefulBuilder(builder:                 // 将需要局部刷新的组件包裹在StatefulBuilder中   
    (BuildContext context, StateSetter setState) {
         wpsStatesetter = setState;      // 将setState复制给自定义的StateSetter
         return Column();
    }
),

// step3
wpsStatesetter(                           // 刷新时调用，任何位置
    () {
        step1ok = true;
    },
);
```

### ValueListenableBuider

```apache
  //录播数据
  //step1
  final ValueNotifier<List> infosNotifier = ValueNotifier<List>([]);
  final ValueNotifier<int> intNotifier = ValueNotifier<int>(0);
  final ValueNotifier<String> intNotifier = ValueNotifier<String>('');

  //step2
  ValueListenableBuilder<List>(
      valueListenable: infosNotifier,
          builder: (context, value, child) {
              return Expanded(
                     child: ...  
                     );
          },
   ),

   //step3
   // 更新infos列表的方法
   void updateInfos(List newInfos) {
    // 确保创建一个新的列表来触发更新
    infosNotifier.value = [...newInfos];
   }

```

### step1: 声明`ValueNotifier`

```
final ValueNotifier<List> infosNotifier = ValueNotifier<List>([]);
```

这里声明了一个`ValueNotifier`，它的泛型参数是`List`，初始值为一个空列表。`ValueNotifier`是一个特殊的对象，它可以存储一个值，并且在值发生变化时通知其监听者。

### step2: 使用`ValueListenableBuilder`

```
ValueListenableBuilder<List>(
    valueListenable: infosNotifier,
    builder: (context, value, child) {
        return Expanded(
               child: ...  
               );
    },
),
```

`ValueListenableBuilder`是一个Widget，它监听`infosNotifier`的变化。当`infosNotifier`的值发生变化时，`builder`函数会被调用，并且传入新的值（在这里是`value`）。`builder`函数的职责是返回一个新的Widget树，这个树将使用新的值来更新UI。

在这个例子中，`Expanded`是一个布局Widget，它将展开其子Widget以填充可用空间。省略号`...`表示这里应该放置实际要显示的子Widget，这个子Widget会根据`infosNotifier`中的列表数据动态更新。

### step3: 更新`infos`列表的方法

```
void updateInfos(List newInfos) {
    // 确保创建一个新的列表来触发更新
    infosNotifier.value = [...newInfos];
}
```

这个方法`updateInfos`接受一个新的列表`newInfos`作为参数，并将其设置为`infosNotifier`的值。关键在于使用扩展运算符`...`来创建一个新的列表。这样做是为了确保当`newInfos`的内容发生变化时，`ValueNotifier`能够检测到这种变化并通知其监听者（即`ValueListenableBuilder`）。

如果直接将`newInfos`赋值给`infosNotifier.value`，而不创建一个新的列表，那么只有当`newInfos`的引用发生变化时，`ValueNotifier`才会触发更新。通过创建一个新的列表，我们确保了即使`newInfos`的内容发生变化，UI也会相应地更新。
