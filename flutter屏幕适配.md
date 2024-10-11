# flutter屏幕适配

### 屏幕适配

```dart

```

`px` getter的目的是将设计稿上的像素尺寸转换为当前设备上的逻辑像素尺寸。设计稿通常是基于某个特定的屏幕分辨率（在这里是1920像素宽）来制作的。当你在应用中使用这些设计稿上的尺寸时，需要将这些尺寸适配到不同分辨率的设备上。

### 代码解释

- `this * window.physicalSize.width / window.devicePixelRatio / 1920.0;`
  
  - `this`: 代表当前调用`px`方法的`double`或`int`值，即设计稿上的像素尺寸。
  - `window.physicalSize.width`: 获取设备屏幕的物理宽度（以像素为单位）。
  - `window.devicePixelRatio`: 获取设备像素比（DPR），即物理像素与逻辑像素的比例。
  - `1920.0`: 假设设计稿是基于1920像素宽的屏幕分辨率。

通过这些值，`px` getter计算并返回一个适配后的逻辑像素尺寸，使得在不同分辨率的设备上都能保持相同的设计比例。

### 使用场景

例如，如果你有一个设计稿上的按钮宽度是100像素，你可以直接在Flutter代码中使用`100.px`来获取适配当前设备屏幕的逻辑像素宽度。

```dart
Button(
  width: 100.px, // 设计稿上的100像素会根据当前设备屏幕适配
  // 其他属性...
)
```

### 注意事项

由于`window`对象已经被标记为弃用，你可能需要将上述代码中的`window.physicalSize.width`和`window.devicePixelRatio`替换为不使用`window`对象的等效方法，如使用`MediaQuery`。

总的来说，这些扩展方法提供了一种便捷的方式来处理Flutter应用中的屏幕适配问题。
