# Transform
&emsp;&emsp;变换组件，可以对child做平移、旋转、缩放等操作。  
## 属性  
属性|描述
:-|:-|
transform|4x4的矩阵。
origin|旋转点，相对于左上角顶点的偏移。默认旋转点事左上角顶点。
alignment|对齐方式。
transformHitTests|点击区域是否也做相应的改变。

* transform  
&emsp;&emsp;如果Transform的child有一个按钮，为true时，
在hitTest点击时会判断点击落点是否在transfrom所做的操作（旋转，拉伸或者位移等）后的区域里面。
直白点就是转换后的按钮仍然可以点击。为false时，点击按钮原来的区域仍然会触发点击事件，但是点击按钮当前区域就不行了。

* 基本使用  
```dart
_item(
  text: '基本使用：',
  press: _baseTrans,
  child: Transform(
    //沿着y轴扭曲/倾斜
    transform: Matrix4.skewY(pi / 180 * _baseTrans._num),
    //相对于坐标系原点的对齐方式
    alignment: Alignment.topRight,
    child: Container(
      color: Colors.lightBlueAccent,
      width: ScreenUtil().setWidth(160),
      height: ScreenUtil().setHeight(56),
      alignment: Alignment.center,
      child: Text('基本使用$num'),
    ),
  )
),
```

![运行示例](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/Transform/1589596415937.gif)

***

&emsp;&emsp;Transform的变换是应用在绘制阶段，而并不是应用在布局(layout)阶段，所以无论对子组件应用何种变化，
其占用空间的大小和在屏幕上的位置都是固定不变的，因为这些是在布局阶段就确定的。  
&emsp;&emsp;Transform还自带了三个便捷的构造函数：  
1. Transform.translate  //平移
2. Transform.rotate     //旋转
3. Transform.scale      //缩放

&emsp;&emsp;[具体使用参考](https://github.com/gneL1/flutter_experiences/blob/master/lib/base_widget/base_widget_transform.dart)  
&emsp;&emsp;[Matrix4的详细使用参考](https://juejin.im/post/5be2fd9e6fb9a04a0e2cace0#comment)




