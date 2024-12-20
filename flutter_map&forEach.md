在 Dart 和 Flutter 中，`forEach` 和 `map` 是两种用于处理集合（如列表）中元素的方法，但它们的用途和执行方式有所不同。

在 Dart 和 Flutter 中，`map` 函数通常用于对集合中的每个元素执行操作，并返回一个新的迭代器。但是，`map` 函数本身并不会对返回的迭代器进行遍历。如果你只是调用 `map` 而没有遍历其结果，那么 `map` 中的函数体实际上并不会执行。

在你给出的代码片段中，`cameras.map(...)` 创建了一个迭代器，其中包含了对每个 `Camera` 对象调用 `httpGetConfig` 方法的 `Future`。但是，由于没有对返回的迭代器进行遍历或等待，这些异步操作实际上没有被触发。

### `map` 的用法

`map` 方法遍历集合中的每个元素，并对每个元素执行一个函数，然后返回一个新的迭代器（在 Dart 中通常是 `Iterable`），其中包含对每个元素应用该函数的结果。`map` 本身不会立即执行任何操作，而是创建一个懒加载的迭代器，这意味着只有在实际遍历这个迭代器时，函数才会被执行。

例如：

```
List<int> numbers = [1, 2, 3];
List<int> doubled = numbers.map((number) => number * 2).toList();
```

在这个例子中，`map` 方法会返回一个新的 `Iterable<int>`，其中包含原始列表中每个数字的两倍。要实际得到这个新列表，我们需要遍历这个 `Iterable`（在这里是通过调用 `toList()` 方法）。

### `forEach` 的用法

`forEach` 方法也是遍历集合中的每个元素，并对每个元素执行一个函数。与 `map` 不同的是，`forEach` 不返回任何值（它返回 `void`），并且它会立即对每个元素执行提供的函数，而不是创建一个新的迭代器。

例如：

```
List<int> numbers = [1, 2, 3];
numbers.forEach((number) {
  print(number);
});
```

在这个例子中，`forEach` 立即遍历列表，并对每个元素执行 `print` 函数。

### 为什么使用 `forEach` 而不是 `map`

在原始问题中，目标是对每个 `Camera` 对象执行一个异步操作，并且不需要从这些操作中收集任何结果。在这种情况下，使用 `forEach` 是合适的，因为：

1. `forEach` 立即对每个元素执行操作，这正是我们想要的，因为我们希望对每个 `Camera` 对象立即发起异步请求。
2. 我们不需要从这些异步操作中收集结果，所以不需要返回一个新的集合或迭代器，这正是 `map` 会做的。

如果我们使用 `map`，我们需要额外步骤来遍历返回的迭代器，以确保每个异步操作都被执行。而 `forEach` 直接为我们完成了这一步。

### 总结

- 使用 `map` 当你需要对集合中的每个元素执行操作，并希望得到一个包含结果的新集合时。
- 使用 `forEach` 当你需要对集合中的每个元素执行操作，但不关心操作的结果，也不需要返回新的集合时。
