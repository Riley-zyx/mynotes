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
