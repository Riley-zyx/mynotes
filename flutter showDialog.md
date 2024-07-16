## 1 自定义dialog

```apache
showDialog(
     barrierColor:
     Colors.black.withOpacity(0), //设置透明度：0就是完全透明，1不透明
     context: context,
     barrierDismissible: true, //点击空白处，自动退出，默认为true
     builder: (BuildContext context) {
         return Container();   //这里定义container大小 比如height 无效，需要用maigin
     }
);
```
