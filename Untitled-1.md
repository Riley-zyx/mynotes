Flutter_组件用途List

1、`Offstage`是否显示

2、在使用 `Container` 的时候我们经常会使用到 *margin* 和 *padding* 参数，其实在上一篇我们已经说过， `Container` 其实只是对各种布局的封装，内部的 *margin* 和 *padding* 其实是通过 `Padding` 实现的，而 `Padding` 不支持负数，所以如果你需要用到负数的情况下，推荐使用 `Transform` 。

```dart
 Transform(
      transform: Matrix4.translationValues(10, -10, 0),
      child: new Container(),
    );
```

#### 3、 控件圆角裁剪

日常开发中我们大致上会使用两种圆角方案：

- 一种是通过 `Decoration` 的实现类 `BoxDecoration` 去实现。
- 一种是通过 `ClipRRect` 去实现。

其中 `BoxDecoration` 一般应用在 `DecoratedBox` 、 `Container` 等控件，这种实现一般都是直接 *Canvas* 绘制时，针对当前控件的进行背景圆角化，并不会影响其 *child* 。这意味着如果你的 *child* 是图片或者也有背景色，那么很可能圆角效果就消失了。

而 `ClipRRect` 的效果就是会影响 *child* 的，比如用ClipRRect包裹视频播放的组件，才会出现圆角。

#### 4、懒加载

Flutter 中通过 `FutureBuilder` 或者 `StreamBuilder` 可以简单的实现懒加载，通过 `future` 或者 `stream` “异步” 获取数据，之后通过 `AsyncSnapshot` 的 data 再去加载数据


