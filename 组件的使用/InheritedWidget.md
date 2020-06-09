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


# 来搭建一个Provider
&emsp;&emsp;先整一个实体：  
```dart
///产品
class _Product{
  double price;
  double count;
  _Product(this.price,this.count);
}
```

&emsp;&emsp;定义实体类的```Model```,继承```ChangeNotifier```来监听和触发 刷新组件这个事件：  
```dart
///产品的Model类
class _ProductModel extends ChangeNotifier{

  List<_Product> _products = [];

  ///设置List不能修改
  UnmodifiableListView<List<_Product>> get products => List.unmodifiable(_products);

  ///获取商品总价
  double get totalPrice => _products.fold(0, (previousValue, element) => previousValue + element.count * element.price);

  ///添加数据
  void addProduct(_Product value){
    _products.add(value);
    notifyListeners();
  }

  @override
  void dispose() {
    // TODO: implement dispose
    super.dispose();
  }
}
```

&emsp;&emsp;整一个类继承```InheritedWidget```,来保存```Model```。由于不知道```Model```是什么类型，所以用泛型。  
```dart
class _MyInheritedWidget<T> extends InheritedWidget{

  final T model;

  _MyInheritedWidget({
    Key key,
    this.model,
    Widget child
  }) : super(key : key,child : child);



  ///必须继承的方法
  ///返回true通知子树中依赖model的Widget
  @override
  bool updateShouldNotify(InheritedWidget oldWidget) {
    // TODO: implement updateShouldNotify
//    throw UnimplementedError();
    return true;
  }

}
```

&emsp;&emsp;定义```Provider```类，通过```Provider```拿到```Model```类，不知道```Model```类具体是哪个类，所以要设置泛型来获取```Model```类，
```Model```类一定继承ChangeNotifier,所以```T extends ChangeNotifier```：  
```dart
class _MyProvider<T extends ChangeNotifier> extends StatefulWidget {
  final T model;
  final Widget child;

  const _MyProvider({ Key key, this.model, this.child }) : super(key: key);

  ///定义静态方法获取继承组件中保存的Model
  static T of<T>(BuildContext context,{bool listen = true}){
    var value = listen ? context.dependOnInheritedWidgetOfExactType<_MyInheritedWidget<T>>() :
    context.getElementForInheritedWidgetOfExactType<_MyInheritedWidget<T>>() ?.widget as _MyInheritedWidget<T>;
    return value.model;
  }

  @override
  _MyProviderState<T> createState() => new _MyProviderState<T>();
}
```


&emsp;&emsp;定义```Provider```的状态类：
```dart
class _MyProviderState<T extends ChangeNotifier> extends State<_MyProvider> {


  ///刷新事件
  void update(){
    setState(() {

    });
  }

  @override
  void didUpdateWidget(_MyProvider<ChangeNotifier> oldWidget) {
    if(widget.model != oldWidget.model){
      oldWidget.model.removeListener(update);
      widget.model.addListener(update);
    }
    // TODO: implement didUpdateWidget
    super.didUpdateWidget(oldWidget);
  }

  ///添加监听器
  @override
  void initState() {
    // TODO: implement initState
    super.initState();
    ///为什么是model来addListener?
    ///因为model继承自ChangeNotifier
    widget.model.addListener(update);
  }

  ///移除监听器
  @override
  void dispose() {
    // TODO: implement dispose
    widget.model.removeListener(update);
    super.dispose();
  }


  @override
  Widget build(BuildContext context) {
    return _MyInheritedWidget<T>(
      child: widget.child,
      model: widget.model,
    );
  }
}
```

&emsp;&emsp;封装成```Contumer```：  
```dart
class _MyConsumer<T extends ChangeNotifier> extends StatelessWidget {


  final Widget child;
  ///注意返回值
  ///builder最后返回的是一个Widget
  final Widget Function(BuildContext context,T value,Widget child) builder;

  const _MyConsumer({
    Key key,
    this.child,
    this.builder,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return  builder(
        context,
        _MyProvider.of<T>(context),
        child
    );
  }
}
```

&emsp;&emsp;使用：  
```dart
@override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('复盘Provider'),
      ),
      body: _MyProvider<_ProductModel>(
        model: _ProductModel(),
        child: Column(
          children: [
            _MyConsumer<_ProductModel>(
              builder: (context, value, child) {
                return Text(value.totalPrice.toString());
              },
            ),

            Builder(builder: (context) {
              print('构建了按钮');
              return FlatButton(
                onPressed: (){
                  _MyProvider.of<_ProductModel>(context,listen: false).addProduct(_Product(20.0, 1));
                },
                child: Text('BT'),
              );
            },),

          ],
        ),
      ),
    );
  }
```
