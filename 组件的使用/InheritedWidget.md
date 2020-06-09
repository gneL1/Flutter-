# InheritedWidget
&emsp;&emsp;数据共享组件，将数据从上到下传递。  

&emsp;&emsp;定义一个数据模型：  
```dart
///数据模型
class _CountModel{
  final int count;//计数器
  final VoidCallback increment;//增长函数
  const _CountModel(this.count,this.increment);
}
```

&emsp;&emsp;定义一个类继承```InheritedWidget```，用来保存数据：  
```dart
class _CountWidget extends InheritedWidget{

  final _CountModel model;

  _CountWidget({
    Key key,
    @required this.model,
    @required Widget child,
  }) : super(key: key, child: child);

  ///提供数据模型的方法
  static _CountWidget of(BuildContext context){
    ///dependOnInheritedWidgetOfExactType会注册依赖关系
    ///在源码里是：
    ///_dependencies ??= HashSet<InheritedElement>();
    ///    _dependencies.add(ancestor);
    ///   ancestor.updateDependencies(this, aspect);
    ///   return ancestor.widget;
    ///当InheritedWidget发生变化时，就会更新依赖它的子孙组件，
    ///也就会调用子孙组件的didChangeDependencies()方法
    ///
    return context.dependOnInheritedWidgetOfExactType<_CountWidget>();

    ///getElementForInheritedWidgetOfExactType不会注册依赖关系
    ///InheritedWidget发生变化时,也就不会更新相应的子孙组件
//    return context.getElementForInheritedWidgetOfExactType<_CountWidget>().widget;
  }
  

  ///该回调决定当data发生变化时，是否该通知子树中依赖data的widget
  @override
  bool updateShouldNotify(_CountWidget oldWidget) {
    // TODO: implement updateShouldNotify
//    throw UnimplementedError();
    return model.count != oldWidget.model.count;
  }

}
```

&emsp;&emsp;使用：  
```dart
class StateRelativeInheritedWidget extends StatefulWidget {
  StateRelativeInheritedWidget({ Key key }) : super(key: key);

  @override
  StateRelativeInheritedWidgetState createState() => new StateRelativeInheritedWidgetState();
}

class StateRelativeInheritedWidgetState extends State<StateRelativeInheritedWidget> {

  int _counter = 0;
  void _incrementCounter(){
    setState(() {
      _counter++;
    });
  }


  @override
  void didChangeDependencies() {
    // TODO: implement didChangeDependencies
    super.didChangeDependencies();
    print('主页面执行了didChangeDependencies');
  }

  @override
  Widget build(BuildContext context) {
    print('刷新了主页面');
    return _CountWidget(
        model: _CountModel(_counter, _incrementCounter),
        child: _WidgetA()
    );
  }
}


class _WidgetA extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('InheritedWidget'),
      ),
      body: _WidgetB(),
      floatingActionButton: _WidgetF(),
    );
  }
}

class _WidgetB extends StatefulWidget {
  _WidgetB({ Key key }) : super(key: key);

  @override
  _WidgetBState createState() => new _WidgetBState();
}

class _WidgetBState extends State<_WidgetB> {

  @override
  void didChangeDependencies() {
    // TODO: implement didChangeDependencies
    super.didChangeDependencies();
    print('执行了WidgetB的didChangeDependencies');
  }

  @override
  Widget build(BuildContext context) {
    print('刷新B页面');
    var _counter = _CountWidget.of(context).model.count;
    // TODO: implement build
    return Center(
      child: Text(
          _counter.toString()
      ),
    );
  }
}

class _WidgetF extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    var _increment = _CountWidget.of(context).model.increment;
    return FloatingActionButton(
      onPressed: (){
        _increment();
      },
      child: Icon(Icons.add),
    );
  }

}
```

&emsp;&emsp;```context.dependOnInheritedWidgetOfExactType```的结果：  
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/InheritedWidget/InheritedWidget_01.PNG)


&emsp;&emsp;```context.getElementForInheritedWidgetOfExactType```的结果：  
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/InheritedWidget/InheritedWidget_02.PNG)
