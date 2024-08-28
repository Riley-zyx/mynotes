# Flutter_AndroidManifest.xml

AndroidManifest.xml文件是Android应用的基本配置文件，它定义了应用的名称、图标、权限、活动和组件等信息。<mark>/android/app/src/main/AndroidManifest.xml</mark>

## 1、根元素和命名空间

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.airych.nbclient">
```

- `<manifest>`：根元素，包含所有其他元素。
- `xmlns:android`：定义Android命名空间，用于引用Android的属性。
- `package`：定义应用的包名，这里是`com.airych.nbclient`。

## 2、权限声明

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.WAKE_LOCK"/>
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>

```

- **网络访问**：`<uses-permission android:name="android.permission.INTERNET"/>`，允许应用访问网络。
- **唤醒锁**：`<uses-permission android:name="android.permission.WAKE_LOCK"/>`，允许应用控制设备唤醒状态。
- **相机访问**：`<uses-permission android:name="android.permission.CAMERA"/>`，允许应用访问设备的摄像头。
- **位置访问**：`<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>` 和 `<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>`，允许应用访问精确和粗略的位置信息。
- **存储访问**：`<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>` 和 `<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>`，允许应用读写外部存储。

### 3、**特性声明**（已被注释掉）：

```xml
<!-- <uses-feature android:glEsVersion="0x00030000" android:required="true"/> -->
```

- 如果没有被注释掉，这个元素会声明应用需要的OpenGL ES版本，这里是0x00030000，代表OpenGL ES 3.0，并且是必须的。

### 4、**应用配置**：

```xml
<application
   android:label="nbclient"
   android:icon="@mipmap/ic_launcher">
```

- `<application>`：定义应用的全局属性。
- **应用标签**：`android:label="gstwebrtc"`，设置应用在设备上的显示名称。
- **应用名称**：`android:name="${applicationName}"`，设置应用的类名，这是一个变量，将在构建（build.gradle）时替换。
- **应用图标**：`android:icon="@mipmap/ic_launcher"`，设置应用图标资源。

### 5、**活动声明**：

```xml
<activity
            android:name=".MainActivity"
            android:exported="true"
            android:launchMode="singleTop"
            android:theme="@style/LaunchTheme"
            android:configChanges="orientation|keyboardHidden|keyboard|screenSize|smallestScreenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
            android:hardwareAccelerated="true"
            android:windowSoftInputMode="adjustResize">
            <!-- Specifies an Android theme to apply to this Activity as soon as
                 the Android process has started. This theme is visible to the user
                 while the Flutter UI initializes. After that, this theme continues
                 to determine the Window background behind the Flutter UI. -->
            <meta-data
              android:name="io.flutter.embedding.android.NormalTheme"
              android:resource="@style/NormalTheme"
              />
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
                
            </intent-filter>
        </activity>
```

- `<activity>`：定义应用中的一个活动（屏幕）。
- `android:name`：活动的类名，这里是`.MainActivity`。
- `android:exported`：表示该活动是否可被其他应用启动，这里设置为`true`。
- `android:launchMode`：定义活动的启动模式，这里是`singleTop`，表示如果活动已经在栈顶，则不会重新创建。
- `android:theme`：定义活动的主题，这里是`@style/LaunchTheme`。
- `android:configChanges`：指定配置更改时不需要重启活动，这里列出了多种配置更改。
- `android:hardwareAccelerated`：表示是否启用硬件加速，这里设置为`true`。
- `android:windowSoftInputMode`：定义软键盘输入区域的显示模式，这里是`adjustResize`。

#### **Intent过滤器**：

```xml
<intent-filter>
   <action android:name="android.intent.action.MAIN"/>
   <category android:name="android.intent.category.LAUNCHER"/>
</intent-filter>
```

- 定义了`MainActivity`作为应用的入口点。

### **6、OAuth2回调活动**：

```xml
<activity android:name="com.linusu.flutter_web_auth_2.CallbackActivity" android:exported="true">
   <intent-filter android:label="flutter_web_auth_2">
       <!-- ... -->
   </intent-filter>
</activity>
```

- 定义了一个用于OAuth2认证的回调活动，并设置了一个Intent过滤器来拦截自定义的uri方案。

### **7、Flutter插件注册元数据**：

```
<meta-data
   android:name="flutterEmbedding"
   android:value="2" />
```

- 用于Flutter工具生成插件注册代码

### **8、查询声明**：

```xml
<queries>
   <intent>
       <action android:name="android.intent.action.PROCESS_TEXT"/>
       <data android:mimeType="text/plain"/>
   </intent>
</queries>
```

- 定义了应用可以查询的其他应用能够处理文本的Intent。

总结来说，这个AndroidManifest.xml文件为名为"nbclient"的应用定义了基本的配置


