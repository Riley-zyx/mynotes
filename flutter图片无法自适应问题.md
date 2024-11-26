flutter自适应屏幕——图片

NO！通过Image.asset()加载的资源文件，直接在里面设置大小无法伸缩图片

YES！通过container/SizedBox嵌套包裹(必须是恰好包裹，否则也无法伸缩适用屏幕）

注意下图的width和height都是根据图片大小进行调整的，要么是w要么是h与图片宽高相同可以限制住图片。

![](/home/riley/Desktop/mynotes/mynotes/images/2024-10-17-11-17-41-image.png)


