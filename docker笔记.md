# docker命令

## 概念：

## 注意点：每一次docker run 都会新建一个容器，每一个容器都是独立的，每次新建的容器都有各自的id。挂载的方式是将电脑的资源与容器共享，千万不要随意删除。

### 1 如何拉取镜像

* 配置镜像源（在安装时，已经配置）
* 命令行执行
* ```
  docker pull  arm64v8/ubuntu:22.04
  ```

### 2 如何通过指定镜像，跑一个容器

```apache
 sudo docker run -it arm64v8/ubuntu:22.04
```

* i: 指
* t: 指

### 3 如何通过下载一个镜像的方式（下载的是一个压缩包，不解压），**把镜像加载上去，然后执行** load方式不接受参数，自己要另外命名

load 和 import 命令的区别：https://blog.csdn.net/qq_33204709/article/details/129339350

```apache
docker load < myimage.tar
docker images   //查看id
docker tag a01baa71e563 mubuntu:flutter-elinux
```
