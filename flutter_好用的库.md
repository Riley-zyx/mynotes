# 库+示例

## 1. 自定义的模拟键盘

pubspec.yaml  

```apache
virtual_keyboard_multi_language: ^1.1.0
```

```dart
Visibility(
                                                              visible:
                                                                  isVisible,   //定义是否可见
                                                              child: Container(
                                                                decoration: BoxDecoration(
                                                                    color: Colors
                                                                        .white,
                                                                    borderRadius: BorderRadius.only(
                                                                        topLeft:
                                                                            Radius.circular(
                                                                                10),
                                                                        topRight:
                                                                            Radius.circular(10))),
                                                                alignment: Alignment
                                                                    .bottomCenter,
                                                                child:
                                                                    VirtualKeyboard(  //主要部分
                                                                  height: 400,
                                                                  fontSize: 24,
                                                                  textColor:
                                                                      Colors
                                                                          .black,
                                                                  textController:
                                                                      _controller,
                                                                  defaultLayouts: [
                                                                    VirtualKeyboardDefaultLayouts
                                                                        .English,
                                                                    VirtualKeyboardDefaultLayouts
                                                                        .Arabic,
                                                                  ],
                                                                  type: isNumericMode
                                                                      ? VirtualKeyboardType
                                                                          .Numeric
                                                                      : VirtualKeyboardType
                                                                          .Alphanumeric,
                                                                  onKeyPress:
                                                                      _onKeyPress,
                                                                ),
                                                              ),
                                                            );
```

## 2. 天气获取

```dart
weather: ^3.1.1
```

```dart
import 'package:weather/weather.dart';
```
