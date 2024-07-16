# NBUI docker 交叉编译

## step1 通过mubuntu:flutter-elinux镜像 打开一个容器

sudo docker run -it --cpus=12 mubuntu:flutter-elinux /bin/bash

## step2 进入home目录ls

## step3 git clone/pull 最新的NBUI（可直接运行）的源码

## step4 flutter-elinux build elinux 

加上--target-backend-type x11，就会编译成对应的；不加默认编译wayland（电脑默认的）

****flutter-elinux build elinux  --target-backend-type x11****

flutter-elinux build elinux --target-arch=arm64
