# journalctl笔记

1. # 概述

journalctl 用来查询 systemd-journald 服务收集到的日志。systemd-journald 服务是 systemd init 系统提供的收集系统日志的服务。journalctl通常用来查询systemd管理的Unit的日志信息。

2. # 脚本配置

前提：将flutter-elinux程序打包，通过脚本，将homepad程序写入系统service里面，然后以service方式启动。

```bash
#!/usr/bin/env bash

#Start homepad
systemctl stop homepad.service >/dev/null 2>&1
systemctl disable homepad.service >/dev/null 2>&1
systemctl reset-failed homepad.service >/dev/null 2>&1


systemd-run --unit homepad.service --description='Homepad service' \
    -p Restart=always \
    --setenv=GST_PLUGIN_PATH=/home/riley/ubuntu2204_arm64_sysroot/usr/lib/aarch64-linux-gnu:/home/riley/ubuntu2204_arm64_sysroot/usr/lib/aarch64-linux-gnu/gstreamer-1.0 \
    --setenv=DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus \
    --setenv=XDG_RUNTIME_DIR=/run/user/1000 \
    --setenv=HOME=/home/riley/Desktop/workspace/NBUI/build/elinux/x64/release/ \
    --working-directory=/home/riley/Desktop/workspace/NBUI/build/elinux/x64/release/bundle/ \
    /home/riley/Desktop/workspace/NBUI/build/elinux/x64/release/bundle/homepad -b . -f
```

`XDG_RUNTIME_DIR=/run/user/1000`

`DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus`

`DBUS_SESSION_BUS_ADDRESS`和`XDG_RUNTIME_DIR`这两个变量的设置要和evtvcr进程以及gstWebRTC进程这两个进程设置一致

- 如何查看进程设置：终端命令`env`

![](https://ai-gardiance.feishu.cn/space/api/box/stream/download/asynccode/?code=OTdlYTMyOTcxMjZmM2Q3MGNlYjcxZDgxMjRkYTZlZDhfc0xzTjA1MTZpZE9QMDFnem82cHFrQkhuUHI0RGdEeUdfVG9rZW46TEpwYmJ4cWwxb0ZyOFd4ZXdrdGM2OURIbjVnXzE3MjU1MTk2NTg6MTcyNTUyMzI1OF9WNA)

3. # journalctl命令

$ journalctl

不带任何选项时，默认输出所有的日志记录

$ journalctl -n [num]

显示最后num行的日志，如果省略num,则默认显示最后10行

$ journalctl -f

实时滚动显示最新日志

$ journalctl -u <unit_name>

显示指定unit的日志，unit_name为具体的unit名称, 如果想合并显示多个unit日志可以用多个-u <unit_name>

$ journalctl -o

设置日志输出的格式，mode的值为(short, short-iso,short-precise, short-monotonic, verbose,export, json, json-pretty, json-sse, ca)

$ journalctl --no-pager

日志默认分页输出,–no-pager改为正常的标准输出

$ journalctl --since

显示从某个时间点之后的数据

journalctl --since=“2020-08-31 10:00:00” #显示2020年8月31日10点以后的数据

journalctl --since “10 min ago” #显示最近10分钟内的日志

journalctl --since today/yesterday #显示今天/昨天以来的日志

journalctl --since “2020-08-31 10:00:00” --until “2020-08-31 11:00:00” #显示某个时间段内的日志(2020-08-31 10点-11点)

4. # flutter中命令格式差异

```dart
  static Future<String> get5minLog() async {    
  //journalctl -u homepad.service --since "5 min ago"    
  var result = await Process.run(      
  'journalctl', // 命令      
  ['-u', 'homepad.service', '--since', '5\ min\ ago'], // 参数列表    );    
  return result.outText;  }
```

```dart
// journalctl -u homepad.service |grep -v "Failed to load plugin" 
// 不显示含“Failed to load plugin”的行

static Future<String> getTodayFilteredLogs() async {
    // 启动 journalctl 进程
    var journalctl = await Process.start(
        'journalctl', ['-u', 'homepad.service', '--since', 'today']);

    // 启动 grep 进程
    var grep = await Process.start('grep', ['-v', 'Failed to load plugin']);

    // 将 journalctl 的输出连接到 grep 的输入
    journalctl.stdout.pipe(grep.stdin);

    // 收集 grep 的输出
    var output = await grep.stdout.transform(utf8.decoder).join();

    // 等待两个进程结束
    var journalExitCode = await journalctl.exitCode;
    var grepExitCode = await grep.exitCode;

    if (journalExitCode != 0) {
      throw Exception(
          'journalctl command failed with exit code $journalExitCode');
    }

    if (grepExitCode != 0) {
      throw Exception('grep command failed with exit code $grepExitCode');
    }

    return output;
  }
```
