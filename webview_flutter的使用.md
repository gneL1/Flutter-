* 需要事先注意的两个点
1. 当使用平台通道代码时(MethodChannel)，会导致所有依赖平台通道的插件崩溃。比如调用了Android端获取电池电量的代码后，webview_flutter插件会报以下错误：

> Unhandled Exception: PlatformException(error, java.lang.IllegalStateException: Trying to create a platform view of unregistered type: plugins.flutter.io/webview E/flutter (14467)

参考：
[https://github.com/flutter/flutter/issues/25714#issuecomment-611100047](https://github.com/flutter/flutter/issues/25714#issuecomment-611100047  "1111")

