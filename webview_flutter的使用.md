# WebView

* 需要事先注意的两个点
1. 当使用平台通道代码时(MethodChannel)，会导致所有依赖平台通道的插件崩溃。比如调用了Android端获取电池电量的代码后，webview_flutter插件会报以下错误：

> Unhandled Exception: PlatformException(error, java.lang.IllegalStateException: Trying to create a platform view of unregistered type: plugins.flutter.io/webview E/flutter (14467)

&emsp;&emsp;参考：
[https://github.com/flutter/flutter/issues/25714#issuecomment-611100047](https://github.com/flutter/flutter/issues/25714#issuecomment-611100047  "1111")

2. WebView无法全屏播放网页视频

* 属性

属性|描述
:-|:-
onWebViewCreated | 在WebView创建完成后调用，只会被调用一次；
initialUrl | 初始load的url；
javascriptMode | JS执行模式（是否允许JS执行）；
javascriptChannels | JS和Flutter通信的Channel；
navigationDelegate | 路由委托（可以通过在此处拦截url实现JS调用Flutter部分）；
gestureRecognizers | 手势监听；
onPageFinished | WebView加载完毕时的回调。


# WebViewController
&emsp;&emsp;通过设置[WebView.onWebViewCreated]可以获得[WebViewController]实例,这里定义一个Completer<WebViewController>,
  通过_controller.future来异步调用WebViewController的方法。

```dart
final Completer<WebViewController> _controller = Completer<WebViewController>();

......
    onWebViewCreated: (WebViewController webViewController){
      _controller.complete(webViewController);
      _controller.future.then((v){
        v.loadUrl('https://www.baidu.com/');
      });
    }
......

```

***


# 加载本地HTML文件
  * 准备一个Html文件放置在自定义的assets文件夹下，记得在yaml文件里声明
  
  ```html
  <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<p>ListView中的webview_flutter要放在SizedBox中，指定并指定sizedbox的高度，否则会出错。
  <span style="color:#e74c3c">实际高度可以调用js来获得返回的高度
  </span>
</p>
<hr />
<p><button onclick="callFlutter()">callFlutter</button></p>
<p id = "p1" style="visibility:hidden;">
Flutter 调用了JS.
Flutter 调用了JS.
Flutter 调用了JS.
</p>
<p><img alt="" src="http://10.0.2.2:8000/main/home_bg.png"  /></p>
<script>
  function callFlutter(){
  //document.location跳转到对应页面
  document.location = "https://www.baidu.com/";
//document.location = "https://cn.bing.com/";
//Toast.postMessage("JS调用了Flutter");
//Toast.postMessage([1,2,);
  }

  function callJS(message){
    document.getElementById("p1").style.visibility = message;
  }
</script>
</html>
```

  * 调用本地文件
```dart
Future localHtml() async{
  String uri = await rootBundle.loadString('assets/testpage.html');
  _controller.future.then((v) async{
    v.loadUrl(Uri.dataFromString(uri,
        mimeType: 'text/html', encoding: Encoding.getByName('utf-8'))
        .toString());
  });
}
```

***


# JS调用Flutter
  ### 1. 使用javascriptChannels发送消息
  * Flutter端：
  
  ```dart
  JavascriptChannel _alertJavascriptChannel(BuildContext context){
    return JavascriptChannel(
      //name属性代表一个协议,js通过name去post对应的信息给Flutter  name.postMessage("xxx")
      name: 'Toast',
      //onMessageReceived为Flutter接收到js的消息后的回调
      //通过message.message来获取js发送的消息内容
      //JavascriptMessage类只有一个String类型的message成员变量
      //传递复杂数据通过传递json字符串来解决
      onMessageReceived: (JavascriptMessage message){
        print('获取到了信息' + message.message);
      }
    );
  }
  
  WebView(
    //记得一定要把javascriptMode打开，不然调用不到flutter端的方法
    javascriptMode: JavascriptMode.unrestricted,
    javascriptChannels: <JavascriptChannel>[
      _alertJavascriptChannel(context)
    ].toSet(),
  )
  
  ```
  
  * Html端：
  > JavascriptChannel中的name要与JS中的name.postMessage()相对应！！
    
```html
<button onclick="callFlutter()">callFlutter</button>

function callFlutter(){
   Toast.postMessage("JS调用了Flutter");
}
```

  ### 2. 使用路由委托navigationDelegate拦截url
  * Flutter端：
  > NavigationDecision.prevent：阻止路由替换；  
  NavigationDecision.navigate：允许路由替换。
  
```dart
WebView(
  //路由委托，可以通过在此处拦截url实现js调用Flutter部分
  //navigationDelegate回调在每次网页路由地址发生变化的时候都会触发
  navigationDelegate: (NavigationRequest request){
    //如果url以'https://'开始，则组织路由跳转并弹出底部sheet，否则路由正常跳转
    if(request.url.startsWith('https://')){
      //弹出底部sheet
      showModalBottomSheet(
        context: context,
        builder: (BuildContext context) {
          return new Container(
            height: 44,
            alignment: Alignment.center,
            child: Text('JS调用了navigationDelegate'),
          );
        },
      ).then((val) {
        print(val);
      });
      //阻止路由替换
      return NavigationDecision.prevent;
    }
    //允许路由替换
    return NavigationDecision.navigate;
  },
)
```

  * Html端：
  
```html
<button onclick="callFlutter()">callFlutter</button>

function callFlutter(){
  //跳转到必应
  document.location = "https://cn.bing.com/";
}

```


***


# Flutter调用JS
  * Flutter端：
  
```dart
onPressed: (){
  //通过WebViewController的evaluateJavascript()调用JS里的callJS()方法
  _controller.future.then((v){
    v
        ?.evaluateJavascript('callJS("visible")')
        ?.then((v){
          print('在Flutter端打印结果$v');
    });

  });
},
```

  * Html端：
```html
  <p id="p1" style="visibility:hidden;">
    Flutter 调用了 JS.
    Flutter 调用了 JS.
    Flutter 调用了 JS.
  </p>
  
function callJS(message){
  //这里将隐藏的段落重新显示
  document.getElementById("p1").style.visibility = message;
}

```
 
    
  
