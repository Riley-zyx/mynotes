# 关于获取文件的排序问题

有时候，再循环读取一个文件夹下的文件时，我们需要提取其中的数据，但是并不能保证现在获取到的文件数据就是在这一次循环所需要的数据，这时候就需要通过异步或者排序来解决。

## 排序方式解决

用于对文件列表 `fileList` 进行排序。该函数的目标是确保图片和视频文件在 `.info` 文件之前被处理。以下是代码的逐行分析：

```dart
fileList.sort((a, b) {
  // 定义排序函数，它接收两个参数 `a` 和 `b`，这两个参数都是文件系统实体（可能是文件或目录）。
  String pathA = a.path; // 获取第一个文件系统实体的路径。
  String pathB = b.path; // 获取第二个文件系统实体的路径。

  // 图片和视频文件排在前面（.info文件）
  // 检查第一个路径是否以图片或视频文件的扩展名结尾。
  if (pathA.endsWith('.jpg') ||
      pathA.endsWith('.jpeg') ||
      pathA.endsWith('.png') ||
      pathA.endsWith('.mp4')) {
    return -1; // 如果是，返回 -1，这意味着 `a` 应该排在 `b` 前面。
  } else if (pathB.endsWith('.jpg') ||
             pathB.endsWith('.jpeg') ||
             pathB.endsWith('.png') ||
             pathB.endsWith('.mp4')) {
    // 如果第一个路径不是图片或视频文件，检查第二个路径是否是图片或视频文件。
    return 1; // 如果是，返回 1，这意味着 `b` 应该排在 `a` 前面。
  }
  // 如果两个路径都不是图片或视频文件，或者都是 `.info` 文件，保持原有顺序。
  return 0; // 返回 0，这意味着 `a` 和 `b` 的顺序不变。
});

for (FileSystemEntity fileSystemEntity in fileList) {
      if (fileSystemEntity.path.endsWith("mp4")) {
        countMp4 = countMp4 + 1;
      }
    }
    for (FileSystemEntity fileSystemEntity in fileList) {
      // print('cameras: $cameras');
      print('newfileSystemEntity: $fileSystemEntity');
      print('newfileSystemEntity.path: ${fileSystemEntity.path.length}');
      if (fileSystemEntity.path.endsWith("jpg") ||
          fileSystemEntity.path.endsWith("jpeg") ||
          fileSystemEntity.path.endsWith("png")) {
        pathImg = fileSystemEntity.path;
        print('pathImg: $pathImg');
      } else if (fileSystemEntity.path.endsWith("mp4")) {
        mp4Path.add(fileSystemEntity.path);
      } else if (fileSystemEntity.path.endsWith("info")) {
        File file = File(fileSystemEntity.path);
        String contents = await file.readAsString();
        // rootBundle.loadString(fileSystemEntity.path).then((contents) {
        // 去除字符串末尾的空白字符
        contents = contents.trimRight();
        print('1212:$contents');
        // 找到第一个 `{` 的位置
        int startIndex = contents.indexOf('{');
        // 找到最后一个 `}` 的位置
        int endIndex = contents.indexOf('}', startIndex);
        // 从第一个 `{` 的位置截取到第一个 `}` 的位置
        String firstJsonString = contents.substring(startIndex, endIndex + 1);
        // 解析第一个 JSON 字符串
        Map firstInfo = json.decode(firstJsonString) ?? {};
        // 找到最后一个 `{` 的位置
        startIndex = contents.lastIndexOf('{');
        // 从 `{` 的位置截取到最后一个字符
        String lastJsonString = contents.substring(startIndex);
        // 解析最后一个 JSON 字符串
        Map info = json.decode(lastJsonString) ?? {};
        print('1212:$info');
        info['datetimeBegin'] = firstInfo['datetime'];
        info['total_duration'] = info['total_duration'];
        info['events'] =
            sessionEventsMap[vcr['vcr_sessionid']]!.toSet().toString();
        info['img'] = pathImg;
        info['mp4Path'] = mp4Path;
        info['number'] = countMp4;
        info['camdevid'] = vcr['camdevid'];
        info['vcr_sessionid'] = vcr['vcr_sessionid'];
        for (Map<String, dynamic> item in cameras) {
          if (item['camdevid'] == vcr['camdevid']) {
            String friendlyName = item['friendlyname'];
            // 找到的 friendlyName
            print("processInfosData::Friendly name: $friendlyName");
            info['friendlyname'] = friendlyName;
            break;
          }
        }

        print('processInfosData::newinfo: $info');
        infos.add(info);
        vcrInfos.add(info);
        updateInfos(infos);
        print('processInfosData::newinfos: $infos');
        // });
      } else {
        print(
            'fileSystemEntity.path.isEmpty? ${fileSystemEntity.path.isEmpty}');
      }
    }
}
```

## 异步方式解决

1. **循环顺序**：只要 `.info` 文件在对应的图片和视频文件之后被处理，那么在处理 `.info` 文件时，`pathImg` 和 `mp4Path` 变量就已经被赋值为正确的路径。这是因为循环是顺序执行的，并且 `.info` 文件是在图片和视频文件之后处理的。

2. **异步加载**：`rootBundle.loadString` 是一个异步操作，它在 `then` 方法中提供了一个回调，这个回调会在文件内容被加载完成后执行。这意味着，即使文件内容加载是异步的，但在 `then` 回调执行时，前面的同步代码已经运行，并且 `pathImg` 和 `mp4Path` 已经被正确赋值。
   
   ```dart
   for (FileSystemEntity fileSystemEntity in fileList) {
         // print('cameras: $cameras');
         print('newfileSystemEntity: $fileSystemEntity');
         print('newfileSystemEntity.path: ${fileSystemEntity.path.length}');
         if (fileSystemEntity.path.endsWith("jpg") ||
             fileSystemEntity.path.endsWith("jpeg") ||
             fileSystemEntity.path.endsWith("png")) {
           pathImg = fileSystemEntity.path;
           print('pathImg: $pathImg');
         } else {
           print(
               'fileSystemEntity.path.isEmpty? ${fileSystemEntity.path.isEmpty}');
         }
         if (fileSystemEntity.path.endsWith("mp4")) {
           mp4Path.add(fileSystemEntity.path);
         }
         if (fileSystemEntity.path.endsWith("info")) {
           rootBundle.loadString(fileSystemEntity.path).then((contents) {
             // 去除字符串末尾的空白字符
             contents = contents.trimRight();
             print('1212:$contents');
             // 找到第一个 `{` 的位置
             int startIndex = contents.indexOf('{');
             // 找到最后一个 `}` 的位置
             int endIndex = contents.indexOf('}', startIndex);
             // 从第一个 `{` 的位置截取到第一个 `}` 的位置
             String firstJsonString = contents.substring(startIndex, endIndex + 1);
             // 解析第一个 JSON 字符串
             Map firstInfo = json.decode(firstJsonString) ?? {};
             // 找到最后一个 `{` 的位置
             startIndex = contents.lastIndexOf('{');
             // 从 `{` 的位置截取到最后一个字符
             String lastJsonString = contents.substring(startIndex);
             // 解析最后一个 JSON 字符串
             Map info = json.decode(lastJsonString) ?? {};
             print('1212:$info');
             info['datetimeBegin'] = firstInfo['datetime'];
             info['total_duration'] = info['total_duration'];
             info['events'] =
                 sessionEventsMap[vcr['vcr_sessionid']]!.toSet().toString();
             info['img'] = pathImg;
             info['mp4Path'] = mp4Path;
             info['number'] = countMp4;
             info['camdevid'] = vcr['camdevid'];
             info['vcr_sessionid'] = vcr['vcr_sessionid'];
             for (Map<String, dynamic> item in cameras) {
               if (item['camdevid'] == vcr['camdevid']) {
                 String friendlyName = item['friendlyname'];
                 // 找到的 friendlyName
                 print("processInfosData::Friendly name: $friendlyName");
                 info['friendlyname'] = friendlyName;
                 break;
               }
             }
   
             print('processInfosData::newinfo: $info');
             infos.add(info);
             vcrInfos.add(info);
             updateInfos(infos);
             print('processInfosData::newinfos: $infos');
           });
         }
   ```

35931a40-4583-11ef-bdc1-25b36fbf1cba

101799b0-48b4-11ef-bdc1-25b36fbf1cba

84d8bef0-508e-11ef-bdc1-25b36fbf1cba

aa8b0e40-5300-11ef-8ac8-cfc1528e289f

c948c970-5ad1-11ef-8ac8-cfc1528e289f

288eae10-2970-11ef-bdc1-25b36fbf1cba

c4dab650-5e05-11ef-8ac8-cfc1528e289f

631bd430-5879-11ef-8ac8-cfc1528e289f
