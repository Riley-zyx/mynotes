# flutter界面传值

## 方法1：回调函数callback

### 调用页面

```dart
showDialog(
     context: context,
     builder: (BuildContext context) {
         return FilterDialog(
         cancelCallBack: () => print('cancel'),
         confirmCallBack: (value) {
         print(value);
      },
     );
 });
```

```dart
// 类定义
class FilterDialog extends StatefulWidget {
  VoidCallback? cancelCallBack;   //不带返回值的回调
  ValueCallback? confirmCallBack; //带一个返回值的回调
  FilterDialog({
    Key? key,
    this.cancelCallBack,
    this.confirmCallBack,
  }) : super(key: key);

  @override
  State<FilterDialog> createState() => _FilterDialogState();
}

class _FilterDialogState extends State<FilterDialog> {
```

#### <mark>参数解析</mark>

```dart
/// Signature of callbacks that have no arguments and return no data.
typedef VoidCallback = void Function();
typedef ValueCallback<T> = void Function(T value);
```

在Dart语言中，`typedef`关键字用于定义一个类型的别名，这可以让代码更加清晰易懂。在你提供的代码行中：

```dart
typedef ValueCallback<T> = void Function(T value);
```

这里做了以下几件事情：

1. `typedef`：这是一个关键字，表示下面的代码将定义一个类型的别名。

2. `ValueCallback<T>`：这是类型别名的名称。`<T>`表示这是一个泛型类型别名，`T`是一个类型参数，可以在使用`ValueCallback`时指定具体的类型。

3. `void Function(T value)`：这是类型别名的具体类型。这里定义了一个函数类型，它具有以下特点：
   
   - 返回类型是`void`，表示这个函数不返回任何值。
   - 函数可以接受一个参数，这个参数的类型是`T`，参数名为`value`。

综合以上，`ValueCallback<T>`是一个类型别名，它代表了一个接受一个类型为`T`的参数并且没有返回值的函数类型。使用这个类型别名，可以让你在定义函数类型变量或者函数参数类型时，不必重复写完整的函数类型定义，从而使代码更加简洁。

#### 使用类型别名：

```dart
class FilterDialog extends StatefulWidget {
  VoidCallback? cancelCallBack;
  ValueCallback? confirmCallBack;
  FilterDialog({
    Key? key,
    this.cancelCallBack,
    this.confirmCallBack,
  }) : super(key: key);
}
```

#### 直接使用函数类型：

```dart
class FilterDialog extends StatefulWidget {
  void Function()? cancelCallBack;
  void Function(dynamic value)? confirmCallBack; // 注意这里使用了 dynamic
  FilterDialog({
    Key? key,
    this.cancelCallBack,
    this.confirmCallBack,
  }) : super(key: key);
}
```

在这两种方式中，`cancelCallBack`和`confirmCallBack`都是可选的回调函数。`cancelCallBack`没有参数且不返回任何值，而`confirmCallBack`接受一个参数并返回`void`。

请注意，在直接使用函数类型时，由于`confirmCallBack`没有指定泛型类型参数，所以这里使用了`dynamic`作为参数类型。



## 方法2：广播

主要逻辑分为广播和接收

一般会在原始基础之上封装新的组件更易用 <mark>FBroadcast改写源码在最下方</mark>

### 定义名字

```dart
//Constants class
Class Constants{
  static const String fb_cam_update = "fb_cam_update"; //摄像头信息更新，包括截图
  static const String fb_cam_evt = "fb_cam_evt"; //摄像头事件
}
```

### 广播

```dart
 FBroadcast.instance().broadcast(Constants.fb_cam_evt, value: event);
 FBroadcast.instance().broadcast(Constants.fb_cam_update);
```

### 接收

由于传过来的event参数是dynamic类型，在用这个数据之前先要转化成我们需要的类型

<mark>EvtvcrEvent de = event; 转化</mark>

```dart
 FBroadcast.instance().register(Constants.fb_cam_update, (event, callback) {
      // refreshScreen();
      refreshcamList();
    });
 FBroadcast.instance().register(Constants.fb_cam_evt, (event, callback) {
      EvtvcrEvent de = event;

      // 创建一个唯一的标识符来跟踪每个 camdevid 和 session_id 组合
      final String uniqueKey = '${de.camdevid}_${de.session_id}';
      // 检查是否已经推送过这个 camdevid 和 session_id 的消息
      Constants.logger
          .i('pushNotification  not yet${pushedNotifications} ${uniqueKey}');
      if (!pushedNotifications.contains(uniqueKey)) {
        var camera = CameraManager().getCameraList()
          ..where((camera) => camera.camdevid == de.camdevid)
          ..toList();
        Constants.logger.i('pushNotification  already ${camera.first} ${de}');
        pushNotification(camera.first, de);
        // 将这个 camdevid 和 session_id 组合添加到已推送集合中
        pushedNotifications.add(uniqueKey);
      }
      // cameras.clear();
      // infos.clear();
      //2.获取所有camera信息(数据库存在camera，才会存在录像，后续根据cameras进行查找筛选)
      cameras = CameraManager().getCameraList();
      VcrManager().getVcrs().then((value) {
        vcrdb = processVCRData(value);
        //获取该用户所有vcr录像目录
        for (Map vcr in vcrdb) {
          //获取不同id摄像头的录像目录
          var sessiondir = Directory(
              '${Constants.vcrPath}${vcr['camdevid']}/${vcr['session_id']}');

          // 检查路径是否存在
          if (sessiondir.existsSync()) {
            // 路径存在，获取文件列表
            processInfosData(vcr, sessiondir.listSync());
          } else {
            // 路径不存在，可以选择打印日志或者进行其他操作
            Constants.logger.r_e('Directory does not exist: $sessiondir');
          }
        }
      });
    }, context: this);
```



响应式编程

1. `T get value => _value;`
   
   - 这是一个getter方法，它定义了一个名为`value`的属性，这个属性的类型是泛型`T`。
   - 当外部代码尝试读取`value`属性时，这个getter方法会被调用，并返回`_value`字段的值。
   - `=>`是Dart中的箭头语法，用于简写单行函数或方法的实现。

2. `late T _value;`
   
   - 这行代码声明了一个名为`_value`的泛型字段，类型为`T`。
   - `late`关键字用于延迟初始化，意味着`_value`字段的初始化可以在构造函数之外进行，或者在首次使用前进行。
   - `late`关键字确保在`_value`被访问之前，它必须已经被初始化，否则会抛出异常。

3. `set value(T newValue) { ... }`
   
   - 这是一个setter方法，它定义了一个名为`value`的属性，用于设置`_value`字段的值。
   - `newValue`参数是setter方法要设置的新的值，它的类型也是泛型`T`。
   - 在setter方法内部，首先检查新的值`newValue`是否与当前`_value`的值相同。如果相同，则不执行任何操作并直接返回。
   - 如果`newValue`与`_value`不同，那么将`newValue`赋值给`_value`字段，并且调用`notifyListeners()`方法。

4. `notifyListeners();`
   
   - 这行代码调用了一个`notifyListeners()`方法，这个方法通常存在于`ChangeNotifier`类中。
   - `notifyListeners()`方法用于通知所有监听这个对象的监听器，对象的状态已经发生了变化。
   - 在Flutter中，这通常用于更新UI，因为当`ChangeNotifier`对象的状态改变时，依赖于该状态的Widgets将会重新构建。

综上所述，这段代码定义了一个可观察的属性`value`，当属性值发生变化时，会通知所有监听器。这种模式在Flutter中非常常见，用于实现响应式编程，其中UI可以根据模型的状态变化自动更新。





```dart
import 'dart:convert';

import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';

export 'stateful.dart';

/// [FBroadcast] 帮助开发者在应用内建立一套高效的广播系统，注册到系统中的接收者，将能接收到任意位置发送的对应类型的消息。
/// 同时，[FBroadcast] 还支持了粘性广播，这将帮助开发这轻松处理一些复杂的通讯场景。
///
/// [FBroadcast] Help developers build an efficient broadcast system in the application. Receivers registered in the system will be able to receive corresponding types of messages sent anywhere.
/// At the same time, [FBroadcast] also supports sticky broadcasting, which will help developers easily handle some complex communication scenarios.
class FBroadcast {
  static bool debug = false;
  static final Map<dynamic, FBroadcast> _broadcastMap = {};
  Map<String, _Notifier<dynamic>>? _map;
  late Map<String, List<_Notifier>> _stickyMap;
  late Map<Object, List<ResultCallback>> _receiverCache;
  String _type = "extra";
  dynamic _key;

  FBroadcast._({String? type}) {
    _type = type ?? "extra";
    _map = {};
    _stickyMap = {};
    _receiverCache = {};
  }

  static FBroadcast _instance = FBroadcast._(type: "system");

  /// 获取 [FBroadcast] 系统实例，已进行注册/广播等操作
  ///
  /// Obtained [FBroadcast] system instance, registered/broadcast and other operations have been performed
  ///
  /// [context] 作用域环境。通过作用域环境获取到广播系统，其有效范围将会被限制在作用域范围内（即 context 环境中）。
  ///           作用域内发送的广播，只能被作用域内注册的接收器所接收。而其它作用域将不受其影响，即使它们使用了相同的 Key。
  ///           context 可以是任意类型的对象实例。
  ///           通过 [FBroadcast.dispose] 可以释放一个广播系统。
  ///           如果 [context] 为 null，将返回全局广播。
  /// [context] Scope environment. The broadcast system is acquired through the scope environment, and its effective scope will be limited within the scope (ie in the context environment).
  ///           Broadcasts sent within the scope can only be received by receivers registered in the scope. The other scopes will not be affected by it, even if they use the same Key.
  ///           context can be any type of object instance.
  ///           A broadcast system can be released through [FBroadcast.dispose].
  ///           If [context] is null, global broadcast will be returned.
  static FBroadcast instance([dynamic context]) {
    if (context == null) {
      return _instance;
    } else {
      if (_broadcastMap.containsKey(context) &&
          _broadcastMap[context] != null) {
        return _broadcastMap[context]!;
      } else {
        FBroadcast newObj = FBroadcast._();
        newObj._key = context;
        _broadcastMap[context] = newObj;
        return newObj;
      }
    }
  }

  /// 接收者可以通过该函数获取消息中的数据
  ///
  /// This function allows the receiver to get the data in the message
  static T? value<T>(String key) {
    if (_textIsEmpty(key) || instance()._map == null) return null;
    var value = instance()._map![key]?.value;
    if (value == null) return null;
    if (!(value is T)) {
      debugPrintStack(
          label: 'Error: value type [${value.runtimeType}] is not [$T]');
    }
    return value;
  }

  _Notifier? _get(String key) {
    if (_map == null) return null;
    if (_textIsEmpty(key)) throw Exception("The key can't be null or empty!");
    if (!_map!.containsKey(key)) {
      _map![key] = _Notifier(null);
    }
    return _map![key];
  }

  List? _getReceivers(Object context) {
    if (_receiverCache[context] == null) {
      _receiverCache[context] = [];
    }
    return _receiverCache[context];
  }

  /// 广播一条 [key] 类型的消息。
  /// 已经注册在系统中的接收者将会接收到本条消息。
  /// 接收者通过 [value] 可以获取到本条消息携带的数据。
  /// [key] - 消息类型
  /// [value] - 消息携带的数据。可以是任意类型或是null。
  /// [callback] - 能够收到接收器返回的消息
  /// [persistence] - 是否持久化消息类型。持久化的消息可以在任意时刻通过 [FBroadcast.value] 获取当前消息的数据包。默认情况下，未持久化的消息类型在没有接收者的时候会被移除，而持久化的消息类型则不会。开发者可以通过 [clear] 函数来移除持久化的消息类型。
  ///
  /// Broadcast a message of type [key].
  /// Recipients already registered in the system will receive this message.
  /// The receiver can get the data carried in this message through [value].
  /// [key] - Message type
  /// [value] - The data carried in the message. Can be any type or null.
  /// [callback] - Able to receive the message returned by the receiver
  /// [persistence] - Whether or not to persist message types. Persistent messages can be retrieved at any time by [FBroadcast. Value] for the current message packet. By default, unpersisted message types are removed without a receiver, while persisted message types are not. Developers can use the [clear] function to remove persistent message types.
  void broadcast(String key,
      {dynamic value, ValueCallback? callback, bool persistence = false}) {
    if (_map == null) return;
    if (_textIsEmpty(key)) return;
    if (persistence && !_get(key)!.persistence) {
      _get(key)!.persistence = true;
    }
    _get(key)!.callback = callback;
    if (value == null || _get(key)!.value == value) {
      _get(key)!.notifyListeners();
    } else {
      _get(key)!.value = value;
    }
  }

  /// 广播一条 [key] 类型的粘性消息。
  /// 如果广播系统中还有没注册该类型的接收者，本条消息将被滞留在系统中。一旦有该类型接收者被注册，本条消息将会被立即发送给接收者。
  /// 如果系统中已经注册有该类型的接收者，本条消息将会被立即发送给接收者。
  /// 接收者通过 [value] 可以获取到本条消息携带的数据。
  /// [key] - 消息类型
  /// [value] - 消息携带的数据。可以是任意类型或是null。
  /// [callback] - 能够收到接收器返回的消息
  /// [persistence] - 是否持久化消息类型。持久化的消息可以在任意时刻通过 [FBroadcast.value] 获取当前消息的数据包。默认情况下，未持久化的消息类型在没有接收者的时候会被移除，而持久化的消息类型则不会。开发者可以通过 [clear] 函数来移除持久化的消息类型。
  ///
  /// Broadcast a sticky message of type [key].
  /// If there are unregistered receivers of this type in the broadcast system, this message will be stuck in the system. Once a recipient of this type is registered, this message will be sent to the recipient immediately.
  /// If this type of receiver is already registered in the system, this message will be sent to the receiver immediately.
  /// The receiver can get the data carried in this message through [value].
  ///
  /// [key] - Message type
  /// [value] - The data carried in the message. Can be any type or null.
  /// [callback] - Able to receive the message returned by the receiver
  /// [persistence] - Whether or not to persist message types. Persistent messages can be retrieved at any time by [FBroadcast. Value] for the current message packet. By default, unpersisted message types are removed without a receiver, while persisted message types are not. Developers can use the [clear] function to remove persistent message types.
  void stickyBroadcast(String key,
      {dynamic value, ValueCallback? callback, bool persistence = false}) {
    if (_map == null) return;
    if (_textIsEmpty(key)) return;
    if (persistence && !_get(key)!.persistence) {
      _get(key)!.persistence = true;
    }
    if (_map!.containsKey(key) && _map![key]!.hasListeners) {
      broadcast(key, value: value, callback: callback);
    } else {
      if (_stickyMap[key] == null) {
        _stickyMap[key] = [];
      }
      _stickyMap[key]!.add(_Notifier(value)..callback = callback);
    }
  }

  /// 注册指定类型的接收者。
  /// 如果传入了 [context] 环境，该接收者将会被注册到环境中。环境可以是任意类型的对象，例如：页面、类..
  /// 接收者通过 [value] 可以获取到本条消息携带的数据。
  /// 当调用 [unregister] 时，该接收者即会被清除。
  /// [key] - 消息类型
  /// [receiver] - 接收者
  /// [context] - 环境。不为null，[receiver] 将会被注册到环境中。
  /// [more] - 方便一次注册多个接收者
  ///
  /// Register recipients of the specified type.
  /// If the [context] environment is passed in, the recipient will be registered in the environment. The environment can be any type of object, for example: page, class...
  /// The receiver can get the data carried in this message through [value].
  /// When [unregister] is called, the receiver will be cleared.
  /// [key]-message type
  /// [receiver] - receiver
  /// [context] - context. Not null, [receiver] will be registered in the environment.
  /// [more] - Make it easy to register multiple recipients at once
  FBroadcast register(
    String key,
    ResultCallback? receiver, {
    Object? context,
    Map<String, ResultCallback>? more,
  }) {
    if (_map == null) return this;
    if (!_textIsEmpty(key) && receiver != null) {
      _get(key)!.addListener(receiver);
      if (context != null && !_getReceivers(context)!.contains(receiver)) {
        _receiverCache[context]!.add(receiver);
      }
      if (_stickyMap[key] != null) {
        _stickyMap[key]!.forEach((element) {
          _Notifier notifier = element;
//          _stickyMap.remove(key);
          broadcast(key, value: notifier.value, callback: notifier.callback);
        });
        _stickyMap.remove(key);
      }
    }
    if (more?.isNotEmpty ?? false) {
      more!.forEach((key, value) {
        _get(key)!.addListener(value);
        if (context != null && !_getReceivers(context)!.contains(value)) {
          _receiverCache[context]!.add(value);
        }
        if (_stickyMap[key] != null) {
          _stickyMap[key]!.forEach((element) {
            _Notifier notifier = element;
//          _stickyMap.remove(key);
            broadcast(key, value: notifier.value, callback: notifier.callback);
          });
          _stickyMap.remove(key);
        }
      });
    }
    return this;
  }

  /// 移除指定的接收者 [receiver]。
  /// 如果指定了 [key]、[context] 将有助于更快的移除指定接收者。
  /// [receiver] - 接收者
  /// [key] - 消息类型
  /// [context] - 环境。
  ///
  /// Remove the specified receiver [receiver].
  /// If [key] and [context] are specified, it will help to remove the specified recipient faster.
  /// [receiver] - receiver
  /// [key]-message type
  /// [context] - context.
  void remove(ResultCallback? receiver, {String? key, Object? context}) {
    if (_map == null) return;
    if (receiver == null) return;
    if (!_textIsEmpty(key)) {
      _get(key!)!.removeListener(receiver);
    } else {
      _map!.forEach((k, value) {
        value.removeListener(receiver);
      });
    }
    _cleanMap();
    if (context != null) {
      _getReceivers(context)!.remove(receiver);
      if (_getReceivers(context)!.isEmpty) {
        _receiverCache.remove(context);
      }
    } else {
      List<Object> needRemove = [];
      _receiverCache.forEach((k, value) {
        value.remove(receiver);
        if (value.isEmpty) needRemove.add(k);
      });
      needRemove.forEach((k) {
        _receiverCache.remove(k);
      });
    }
  }

  /// 移除广播系统中，注册到 [context] 环境内的所有接收者。
  /// 例如，在页面关闭时，开发者可以通过 [unregister] 一次性移除注册到该环境内的所有接收者。
  /// 当然，前提时在接收者通过 [register] 注册的时候，传入 [context]，将接收者注册到该环境中。
  /// [context] - 环境。
  ///
  /// Remove all receivers registered in the [context] environment from the broadcast system.
  /// For example, when the page is closed, the developer can use [unregister] to remove all recipients registered in the environment at once.
  /// Of course, the prerequisite is that when the receiver registers through [register], pass in [context] to register the receiver to the environment.
  /// [context] - context.
  void unregister(Object context, {bool async = false}) {
    if (async) {
      _unregisterAsync(context);
    } else {
      _unregister(context);
    }
  }

  void _unregister(Object context) {
    if (_map == null) return;
    if (context != null) {
      for (ResultCallback listener in _getReceivers(context)!) {
        _map!.forEach((key, notifier) {
          notifier.removeListener(listener);
        });
      }
      _cleanMap();
      _getReceivers(context)!.clear();
      _receiverCache.remove(context);
    }
  }

  /// 异步解注册，防止注册过多导致解注册时卡顿
  Future<bool> _unregisterAsync(Object context) async {
    if (_map == null) return false;
    if (context != null) {
      List notifys = _map!.values.toList();
      for (ResultCallback listener in _getReceivers(context)!) {
        for (_Notifier notify in notifys) {
          await Future.delayed(Duration(milliseconds: 0));
          if (notify._listeners != null) {
            notify.removeListener(listener);
          }
        }
      }
      _cleanMap();
      _getReceivers(context)!.clear();
      _receiverCache.remove(context);
    }
    return true;
  }

  /// 移除没有接收器，且不持久化的 [_Notifier]
  ///
  /// Removes a [Notifier] that does not have a receiver and is not persistent
  void _cleanMap() {
    if (_map == null) return;
    List<String> needRemove = [];
    _map!.forEach((key, value) {
      if (!value.hasListeners && !value.persistence) {
        needRemove.add(key);
      }
    });
    needRemove.forEach((key) {
      clear(key);
    });
  }

  /// 移除广播系统中的指定 [key] 类型的所有接收者，以及该类型的粘性广播。
  /// [key] - 类型
  ///
  /// Remove all receivers of the specified [key] type in the broadcast system and sticky broadcasts of that type.
  ///  [key] - type
  void clear(String key) {
    if (_map == null) return;
    _Notifier? remove = _map!.remove(key);
    if (remove?.hasListeners ?? false) {
      remove!.listeners.forEach((receiver) {
        _receiverCache.forEach((key, value) {
          value.remove(receiver);
        });
      });
      List<Object> needRemove = [];
      _receiverCache.forEach((k, value) {
        if (value.isEmpty) needRemove.add(k);
      });
      needRemove.forEach((k) {
//        print('needRemove = $k');
        _receiverCache.remove(k);
      });
//      print('size = ${_receiverCache.length}');
    }
    remove?.dispose();
    _stickyMap.remove(key);
  }

  /// 会移除广播系统中的所有的接收者，以及粘性广播。
  ///
  /// Remove all receivers in the broadcasting system, and sticky broadcasting.
  void dispose() {
    if (_map == null) return;
    // _map.forEach((key, value) {
    //   value.dispose();
    // });
    _map!.clear();
    _receiverCache.clear();
    _stickyMap.clear();
    if (_type == "extra" && _key != null) {
      _broadcastMap.remove(_key);
    }
  }

  /// 输出 FBroadcast 系统中的驻留广播信息
  static void printFBroadcastInfo() {
    if (FBroadcast.debug) {
      _printFBroadcastInfo(fBroadcast: FBroadcast.instance());
      FBroadcast._broadcastMap.forEach((key, value) {
        _printFBroadcastInfo(context: key, fBroadcast: value);
      });
    }
  }

  static void _printFBroadcastInfo({dynamic context, FBroadcast? fBroadcast}) {
    if (fBroadcast != null) {
      int total1 = 0;
      Map reciverInfos1 = {};
      fBroadcast._map!.forEach((key, value) {
        int count = value._listeners?.length ?? 0;
        total1 += count;
        reciverInfos1[key] = {
          "count": count,
        };
      });
      int total2 = 0;
      Map reciverInfos2 = {};
      fBroadcast._stickyMap.forEach((key, value) {
        int count = value.length;
        total2 += count;
        reciverInfos2[key] = {
          "count": count,
        };
      });
      String tag = context == null ? "【系统级】" : "【${context.toString()}级】";
      if (reciverInfos1.isEmpty && reciverInfos2.isEmpty) {
        _fdebugprint("$tag当前系统中无驻留广播");
      } else {
        if (reciverInfos1.isNotEmpty) {
          _fdebugprint(
              "$tag当前驻留系统的[普通广播]，共 $total1 条：${jsonEncode(reciverInfos1)}");
        }
        if (reciverInfos2.isNotEmpty) {
          _fdebugprint(
              "$tag当前驻留系统的[Sticky 广播]，共 $total2 条：${jsonEncode(reciverInfos2)}");
        }
      }
    }
  }
}

/// --------------------------------------------------------------------------------
bool _textIsEmpty(String? text) {
  return text == null || text.length == 0;
}

typedef ValueCallback<T> = void Function(T value);
typedef ResultCallback<T> = void Function(T value, ValueCallback? callback);

class _Notifier<T> {
  bool persistence;
  ValueCallback? callback;

  T get value => _value;
  late T _value;

  set value(T newValue) {
    if (_value == newValue) return;
    _value = newValue;
    notifyListeners();
  }

  ObserverList<ResultCallback>? _listeners = ObserverList<ResultCallback>();

  _Notifier(
    value, {
    this.persistence = false,
  }) {
    _value = value;
  }

  bool _debugAssertNotDisposed() {
    assert(() {
      if (_listeners == null) {
        throw FlutterError('A $runtimeType was used after being disposed.\n'
            'Once you have called dispose() on a $runtimeType, it can no longer be used.');
      }
      return true;
    }());
    return true;
  }

  ObserverList<ResultCallback> get listeners {
    return _listeners!;
  }

  bool get hasListeners {
    assert(_debugAssertNotDisposed());
    return _listeners!.isNotEmpty;
  }

  void addListener(ResultCallback listener) {
    assert(_debugAssertNotDisposed());
    _listeners!.add(listener);
  }

  void removeListener(ResultCallback listener) {
    assert(_debugAssertNotDisposed());
    _listeners!.remove(listener);
  }

  void dispose() {
    assert(_debugAssertNotDisposed());
    _listeners = null;
  }

  void notifyListeners() {
    assert(_debugAssertNotDisposed());
    if (_listeners != null) {
      final List<ResultCallback> localListeners =
          List<ResultCallback>.from(_listeners!);
      for (final ResultCallback listener in localListeners) {
        try {
          if (_listeners!.contains(listener)) listener(value, callback);
        } catch (exception) {}
      }
      callback = null;
    }
  }

  @override
  String toString() => '${describeIdentity(this)}($value)';
}

_fdebugprint(String msg, {String tag = "FBroadcast: "}) {
  if (FBroadcast.debug) {
    var dateTime = DateTime.now();
    String r = "[$dateTime(${dateTime.millisecondsSinceEpoch})] $tag $msg";
    if (r.length < 800) {
      print(r);
    } else {
      _segmentationprint(r);
    }
  }
}

void _segmentationprint(String msg) {
  print("------- split log start -------");
  var outStr = StringBuffer();
  for (var index = 0; index < msg.length; index++) {
    outStr.write(msg[index]);
    if (index % 800 == 0 && index != 0) {
      print(outStr);
      outStr.clear();
      var lastIndex = index + 1;
      if (msg.length - lastIndex < 800) {
        var remainderStr = msg.substring(lastIndex, msg.length);
        print(remainderStr);
        break;
      }
    }
  }
  print("------- split log end -------");
}

```










