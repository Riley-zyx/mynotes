# Flutter_异步操作：广播流

 Dart中的`StreamController`用于<mark>创建</mark>一个可以多次订阅的<mark>广播（broadcast）流</mark>。

- `StreamController`是一个控制器，用于创建广播流，广播流可以被多次订阅。
- 广播流在没有订阅者监听时应该是不活跃的（inert），这意味着它<mark>不会</mark>在没有用户监听流时<mark>泄漏资源</mark>，例如Websockets。
- 广播流不会在没有监听者时缓存事件。
- 控制器通过在调用`add`、`addError`或`close`时，将事件分发给所有当前订阅的监听者。
- 在前一个`add`、`addError`或`close`调用<mark>返回之前</mark>，不允许调用这些方法，因此不会有内部事件队列。
- 每个监听者<mark>订阅都是独立处理</mark>的，如果一个监听者暂停，只有暂停的监听者会受到影响，暂停的监听者会在暂停期间在内部缓存事件，直到恢复或取消。
- 如果`sync`参数设置为`true`，则事件可能会在`add`、`addError`或`close`调用期间由流的订阅者直接触发。在这种情况下，返回的流控制器是`SynchronousStreamController`，必须小心使用以遵守`Stream`合同。关于何时可以使用同步分派的一些解释可以参考`Completer.sync`。
- 如果`sync`参数设置为`false`，事件将始终在稍后的时间触发，即在添加事件的代码完成后。在这种情况下，不保证多个监听者何时接收事件，但保证每个监听者都会按照正确的顺序接收所有事件。<mark>每个订阅都独立处理事件</mark>。
- `onListen`回调在第一个监听者订阅时调用，而`onCancel`在没有活动监听者时调用。如果稍后再次添加监听者，`onListen`将再次被调用。

最后，`StreamController.broadcast`是用于创建广播流控制器的工厂构造函数，它可以根据需要创建同步或异步广播流控制器。同步广播流控制器在事件添加期间可以立即触发事件，而异步广播流控制器会稍后触发事件。
