# PageRouteBuilder
&emsp;&emsp;与MaterialPageRoute、CupertinoPageRoute一样继承自PageRoute，PageRoute类是一个抽象类继承抽象类ModalRoute,
MaterialPageRoute适用于Android平台，跳转页面时的动画是向上打开。CupertinoPageRoute用于IOS平台，跳转动画是向左打开。
PageRouteBuilder则用于自定义路由页面。

## PageRouteBuilder的属性
```dart
PageRouteBuilder(
  //要跳转的页面
  pageBuilder: (_,__,___){
    return Container();
  },
  
  //不透明，是否可以看见底下的页面，为true时看不见
  opaque: false,
  
  //背景颜色
  barrierColor: Color(0x4D000000),
  
  //点击背景时是否可以关闭页面
  barrierDismissible: true,
  
  //打开页面时动画持续时间
  transitionDuration: Duration(milliseconds: 0),
  
  //是否保持打开页面的状态。
  //为true时保持状态，当从打开的弹窗A跳转到另一个页面B后，关闭B页面，A之前所保存的数据任然存在
  maintainState: false,
  
  //为true时，如果打开页面有Scaffold,那么其appBar上的按钮是"❌"符号，为false时则是"⬅"符号
  fullscreenDialog: false,
  
  //是barrier的semanticsLabel语义标签，当barrierDismissible可以关闭(true)且在打开读屏功能时，
  //触摸barrier会读出barrierLabel
  barrierLabel: '点击可关闭',
  
  //RouteSettings,可以设置路由的名字和参数
  settings: RouteSettings(
      name: Router.Demo1,
      arguments: '参数'
  ),
  
  //设置路由的动画
  //transitionsBuilder:
),
```

## 做一个不需要context的路由跳转
```dart
// 先新建一个navigatorKey
GlobalKey<NavigatorState> navigatorKey = GlobalKey<NavigatorState>();

//然后，找到我们的MaterialApp
MaterialApp(
    navigatorKey: navigatorKey,//加上此配置
    title: 'Flutter Demo',
    theme: ThemeData.light(),
    home: HomePage(),
)

//在页面里跳转
navigatorKey.currentState.pushNamed('new_page');
```
&emsp;&emsp;这里有个问题，虽然有navigatorKey.currentContext这个属性，但将其用来跳转弹窗则会报错  
> Log:  The context used to push or pop routes from the Navigator 
must be that of a widget that is a descendant of a Navigator widget.  

&emsp;&emsp;查看源码

> showDialog->showGeneralDialog->Navigator.of(context, rootNavigator: useRootNavigator).pushxxx

```dart
static NavigatorState of(
    BuildContext context, {
    bool rootNavigator = false,
    bool nullOk = false,
  }) {
    final NavigatorState navigator = rootNavigator
        ? context.findRootAncestorStateOfType<NavigatorState>()
        : context.findAncestorStateOfType<NavigatorState>();
    assert(() {
      if (navigator == null && !nullOk) {
        throw FlutterError(
          'Navigator operation requested with a context that does not include a Navigator.\n'
          'The context used to push or pop routes from the Navigator must be that of a '
          'widget that is a descendant of a Navigator widget.'
        );
      }
      return true;
    }());
    return navigator;
  }
  ```
这里的 
> findRootAncestorStateOfType<NavigatorState>和findAncestorStateOfType<NavigatorState>

表示从叶子节点，通过context向上查找根组件对象的，这里也就是寻找NavigatorState对象。但从对应的实现来看，
这两种方式查找初始值都是Element ancestor = _parent;，也就是从parent开始找，
而当前的context就是这个state，他的parent自然是再也找不到了。因此这种方式是不行了。
但在showGeneralDialog中

> Navigator.of(context, rootNavigator: useRootNavigator).push<T>(_DialogRoute<T>(xxx

可见弹窗也是一个路由页面，因此可以用上上面的PageRouteBuilder来自定义弹窗。

## 做一个无context的弹窗
```dart
Router.navigatorKey.currentState.push(
  _CustomDialogRoute(
      pageBuilder: (_,__,___){
        return CustomPage();
      },
    barrierColor:color == null ? Color(0x4D000000) : color,
    barrierDismissible: barrierDismissible,
    transitionDuration: Duration(milliseconds: 300),
  ),
 );
 ```
 &emsp;&emsp;然而实际上用起来会发现，弹窗的阴影背景往往会在路由动画结束后才显示，而不是跟随路由动画一同显示。
 而 **showGeneralDialog()** 调用弹窗时阴影背景确实是跟随路由动画一同出现的。在上面 **showGeneralDialog** 的源码中
 可见其push的是一个 **_DialogRoute** ,**_DialogRoute**继承**PopupRoute**,**PopupRoute**是一个抽象类继承**ModalRoute**,因此可以
 自定义一个_DialogRoute来解决弹窗阴影不同步的问题。
 ```dart
 class _CustomDialogRoute<T> extends PopupRoute<T> {
  _CustomDialogRoute({
    @required RoutePageBuilder pageBuilder,
    bool barrierDismissible = true,
    String barrierLabel,
    Color barrierColor = const Color(0x80000000),
    Duration transitionDuration = const Duration(milliseconds: 200),
    RouteTransitionsBuilder transitionBuilder,
    RouteSettings settings,
  }) : assert(barrierDismissible != null),
        _pageBuilder = pageBuilder,
        _barrierDismissible = barrierDismissible,
        _barrierLabel = barrierLabel,
        _barrierColor = barrierColor,
        _transitionDuration = transitionDuration,
        _transitionBuilder = transitionBuilder,
        super(settings: settings);

  final RoutePageBuilder _pageBuilder;

  @override
  bool get barrierDismissible => _barrierDismissible;
  final bool _barrierDismissible;

  @override
  String get barrierLabel => _barrierLabel;
  final String _barrierLabel;

  @override
  Color get barrierColor => _barrierColor;
  final Color _barrierColor;

  @override
  Duration get transitionDuration => _transitionDuration;
  final Duration _transitionDuration;

  final RouteTransitionsBuilder _transitionBuilder;

  @override
  Widget buildPage(BuildContext context, Animation<double> animation, Animation<double> secondaryAnimation) {
    return  _pageBuilder(context, animation, secondaryAnimation);
  }

  @override
  Widget buildTransitions(BuildContext context, Animation<double> animation, Animation<double> secondaryAnimation, Widget child) {
    if (_transitionBuilder == null) {
      return FadeTransition(
          opacity: CurvedAnimation(
            parent: animation,
            curve: Curves.linear,
          ),
          child: child);
    } // Some default transition
    return _transitionBuilder(context, animation, secondaryAnimation, child);
  }
}

//弹窗跳转
Router.navigatorKey.currentState.push(
  _CustomDialogRoute(
      pageBuilder: (_,__,___){
        return route;
      },
  )
);
```


