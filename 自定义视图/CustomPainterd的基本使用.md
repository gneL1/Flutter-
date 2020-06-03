# 基本概念
**1. Canvas**  
&emsp;&emsp;画布，一个矩形区域，提供了点、线、矩形、圆形等图像的绘制方法。

**2. Paint**  
&emsp;&emsp;画笔，可以设置抗锯齿、颜色、粗细、填充模式等属性，画笔可以定义多个。  

**3. 坐标系**  
&emsp;&emsp;原点(0,0)位于左上角，向右x轴变大，向下y轴变大。  

**4. 绘制流程**
1. 继承CustomPainter并重写paint方法和shouldRepaint方法。  
2. 在写paint方法中绘制内容。  
3. 使用CustomPaint来构建Widget。

***

# Paint的属性
```dart
Paint _paint = new Paint()
// 画笔颜色
..color = Colors.red 
// 画笔笔触类型
// round-画笔笔触呈半圆形轮廓开始和结束
// butt-笔触开始和结束边缘平坦，没有外延
// square-笔触开始和结束边缘平坦,向外延伸长度为画笔宽度的一半
..strokeCap = StrokeCap.round
//是否启动抗锯齿
..isAntiAlias = true
//绘画风格，默认为填充，有fill和stroke两种
..style=PaintingStyle.fill
..blendMode=BlendMode.exclusion//颜色混合模式
..colorFilter=ColorFilter.mode(Colors.blueAccent, BlendMode.exclusion)//颜色渲染模式
..maskFilter=MaskFilter.blur(BlurStyle.inner, 3.0)//模糊遮罩效果
..filterQuality=FilterQuality.high//颜色渲染模式的质量
..strokeWidth = 15.0;//画笔的宽度
```

***

# 前期准备
&emsp;&emsp;声明一种回调类型，将```paint```方法暴露出来
```dart
//声明回调类型
typedef ValuePainter<T,S> = void Function(T value1,S value2);

//自定义的painter类
class MyBasePainter extends CustomPainter{
  final ValuePainter<Canvas,Size> paintCallBack;

  const MyBasePainter({
    this.paintCallBack
  });


  @override
  void paint(Canvas canvas, Size size) {
    // TODO: implement paint
    paintCallBack(canvas,size);
  }

  @override
  bool shouldRepaint(CustomPainter oldDelegate) {
    // TODO: implement shouldRepaint
//    throw UnimplementedError();
    return true;
  }

}


//使用RepaintBoundary能减少额外的重绘，提升性能
RepaintBoundary(
  child: CustomPaint(
    painter: MyBasePainter(
      paintCallBack: (Canvas canvas, Size size){
        ......
      }
    )
  ),
),


```

**1. 绘制点**  
```dart 
List<Offset> points = [
  Offset(0, 0),
  Offset(30, 50),
  Offset(20, 80),
  Offset(100, 40),
  Offset(150, 90),
  Offset(60, 110),
  Offset(260, 160),
];
canvas.drawPoints(PointMode.points, points, _paint);
```
PointMode是一个枚举类：  
```dart
enum PointMode {
  points,// 绘制点
  lines,// 绘制点，且数组内隔点相连，如1-2相连，3-4相连。如最后只剩下一个点，则不去绘制该点
  polygon,// 数组内相邻点连接
}
```

**2. 绘制线**  
```dart
var _startPoint = Offset(30, 30);// 起始点
var _endPoint = Offset(100, 170);// 终点
canvas.drawLine(_startPoint, _endPoint, _paint);
```

**3. 绘制矩形**  
&emsp;&emsp;创建矩形  
```dart

///左上右下绘制矩形
var _rectLTRB = Rect.fromLTRB(0, 0, _length, _length);

///左上长宽绘制矩形
var _rectLTWH = Rect.fromLTWH(_length + 20, 0, _length, _length);

///半径加中心点绘制矩形，只能说正方形
var _rectCir = Rect.fromCircle(center: Offset(_length / 2, _length*2),radius: _length / 2);

///左上角和右下角坐标绘制矩形
var _rectPoints = 
Rect.fromPoints(Offset(_length + 20, _length + 20),Offset(_length * 2 + 20, _length * 2 + 20));
```

&emsp;&emsp;绘制矩形  
```dart
void drawRect(Rect rect, Paint paint)
```

**4. 绘制圆角矩形**  
```dart
void drawRRect(RRect rrect, Paint paint)
```


&emsp;&emsp;这里的```circular```，实际上就是从圆角原点到另外两条边的距离。如果设置矩形的半径和圆角的半径相等，则绘制出来的是一个圆。
用```elliptical```可以体会更深刻。  
```
// 顶点X轴Y轴的圆角设置相同
const Radius.circular(double radius)
// 顶点X轴Y轴的圆角设置可以不相同
const Radius.elliptical(this.x, this.y)

///单个角设置
canvas.drawRRect(
    RRect.fromLTRBAndCorners(0 , 0, _length, _length,
      bottomRight: Radius.circular(_length / 4),
//    bottomLeft: Radius.circular(_length / 2),
//    topLeft: Radius.circular(_length / 2),
//    topRight: Radius.circular(_length / 2),
    ),
    _paint
);

///统一设置
canvas.drawRRect(RRect.fromLTRBR(
    0, _length + 20, _length, _length * 2 + 20, 
    Radius.circular(10)),
    _paint
);
```

**5. 绘制双圆角矩形**  
```dart
void drawDRRect(RRect outer, RRect inner, Paint paint)
```

&emsp;&emsp;需要注意，内圆角矩形的半径不能大于外圆角矩形的半径，否则无法绘制。
```dart
var _length = size.height / 5 * 2;
RRect _rRectOut;
RRect _rRectInner;
///外圆角矩形的半径大于内圆角矩形半径
Rect _rectOut1 = Rect.fromCircle(center: Offset(_length/2, _length / 2),radius: _length / 2);
Rect _rectInner1 = Rect.fromCircle(center: Offset(_length / 2, _length / 2),radius: _length / 3);

_rRectOut = RRect.fromRectAndRadius(_rectOut1, Radius.circular(10));
_rRectInner = RRect.fromRectAndRadius(_rectInner1, Radius.circular(30));
canvas.drawDRRect(_rRectOut, _rRectInner, _paint);

///外圆角矩形的半径等于内圆角矩形半径
Rect _rectOut2 = Rect.fromCircle(center: Offset(_length * 2, _length / 2),radius: _length / 2);
Rect _rectInner2 = Rect.fromCircle(center: Offset(_length  * 2, _length / 2),radius: _length / 2);

_rRectOut = RRect.fromRectAndRadius(_rectOut2, Radius.circular(10));
_rRectInner = RRect.fromRectAndRadius(_rectInner2, Radius.circular(30));
canvas.drawDRRect(_rRectOut, _rRectInner, _paint);

///外圆角矩形的半径大于内圆角矩形半径
Rect _rectOut3 = Rect.fromCircle(center: Offset(_length * 2, _length * 2),radius: _length / 2);
Rect _rectInner3 = Rect.fromCircle(center: Offset(_length  * 2, _length * 2),radius: _length / 2 + 1);

_rRectOut = RRect.fromRectAndRadius(_rectOut3, Radius.circular(10));
_rRectInner = RRect.fromRectAndRadius(_rectInner3, Radius.circular(30));
canvas.drawDRRect(_rRectOut, _rRectInner, _paint);
 ```

**6. 绘制圆**  
```dart
void drawCircle(Offset c, double radius, Paint paint)
```

&emsp;&emsp;注意，设置```paint.style = PaintingStyle.fill```，是一个填充圆。```paint.style = PaintingStyle.stroke```则是一个圆框。  
```dart
var _length = size.height / 5 * 2;
//圆框
_paint.style = PaintingStyle.stroke;
canvas.drawCircle(Offset(_length, _length), _length / 2, _paint);

//填充
_paint.style = PaintingStyle.fill;
canvas.drawCircle(Offset(_length * 2, _length * 2), _length / 2, _paint);
```

**7. 绘制椭圆**  
```dart
canvas.drawOval(rect, _paint);
```

```dart
//椭圆宽度大于高度
Rect rect1 = Rect.fromPoints(Offset(10, 10), Offset(100, 80));
canvas.drawOval(rect1, _paint);

//椭圆宽度小于高度
Rect rect2 = Rect.fromPoints(Offset(10, 110), Offset(80, 200));
canvas.drawOval(rect2, _paint);

//椭圆宽度等于高度
Rect rect3 = Rect.fromPoints(Offset(100, 100), Offset(160, 160));
canvas.drawOval(rect3, _paint);
```

**8. 绘制圆弧**  
```dart
// rect：矩形区域
// startAngle：开始的弧度
// sweepAngle：扫过的弧度，正数顺时针，负数逆时针
// useCenter：是否使用中心点绘制
void drawArc(Rect rect, double startAngle, double sweepAngle, bool useCenter, Paint paint)
```

&emsp;&emsp;角度和弧度的换算：  
角度|弧度
:-|:-|
0°|0
30°|pi / 6
45°|pi / 4
90°|pi / 2
180°|pi
360°|pi * 2

&emsp;&emsp;注意，默认开始弧度是右边中心  

```dart
var rect = Rect.fromCircle(center: Offset(60, 60),radius: 50);
canvas.drawRect(rect, _paint);
//开始度数为0度，顺时针旋转60度
canvas.drawArc(rect, 0, pi / 3, false, _paint);

rect = Rect.fromCircle(center: Offset(180, 60),radius: 50);
canvas.drawRect(rect, _paint);
//开始度数为-180度，逆时针旋转45度
canvas.drawArc(rect, -pi, -pi / 2, false, _paint);

rect = Rect.fromCircle(center: Offset(60, 170),radius: 50);
canvas.drawRect(rect, _paint);
//开始度数为0度，顺时针旋转45度，使用中心点绘制
canvas.drawArc(rect, 0, pi / 2, true, _paint);
```

**9. 绘制阴影**  
```dart
//path:绘制阴影的路径
//color:阴影的颜色
//elevation:阴影高度
//transparentOccluder:是否透明封堵器(阴影范围是否填充满)。通常需要设置为true
void drawShadow(Path path, Color color, double elevation, bool transparentOccluder)
```

```dart
Path path = Path()..addRect(
  Rect.fromCircle(
    center: Offset(size.width / 2,size.height / 2),
    radius: 60,
  )
);
canvas.drawShadow(path, Colors.grey, 4, true);
```

**10. 平移画布**  
```dart
void translate(double dx, double dy)
```

```dart
canvas.drawCircle(Offset(60, 60), 50, _paint);
//x轴平移120
canvas.translate(120, 0);
canvas.drawCircle(Offset(60, 60), 50, _paint);
```

**11. 缩放画布**  
&emsp;&emsp;如果不填```y```值，则```y```轴缩放比例和```x```轴一致。
```dart
void scale(double sx, [double sy]) => _scale(sx, sy ?? sx);
```

```dart
//充满画布的矩形
canvas.drawRect(
    Rect.fromLTWH(0, 0, size.width, size.height), 
    Paint()..color = Colors.orange[100].withOpacity(0.5)
);

canvas.scale(0.5);

//画布缩小为之前的四分之一
canvas.drawRect(
    Rect.fromLTWH(0, 0, size.width, size.height),
    Paint()..color = Colors.blueAccent[100].withOpacity(0.5)
);
```

**12. 旋转画布**  
```dart
void rotate(double radians)
```

```dart
canvas.drawRect(
    Rect.fromLTWH(60, 60, 140, 60),
    Paint()..color = Colors.orange.withOpacity(0.5)
);

canvas.rotate(pi / 6);

canvas.drawRect(
    Rect.fromLTWH(60, 60, 140, 60),
    Paint()..color = Colors.orange.withOpacity(0.5)
);
```

&emsp;&emsp;这里旋转后图像不对劲，是因为旋转的中心点是画布的左上角(0,0)的位置。  
&emsp;&emsp;解决方法：先将画布原点平移到图像中心点，旋转之后再移回去。
```dart
///左上角开始(60,60),长140宽60
canvas.drawRect(
    Rect.fromLTWH(60, 60, 140, 60),
    Paint()..color = Colors.orange.withOpacity(0.5)
);

///移动到图像中心
canvas.translate(130 , 90);
canvas.rotate(pi / 6);

///移回去
canvas.translate(-130 , -90);
canvas.drawRect(
    Rect.fromLTWH(60, 60, 140, 60),
    Paint()..color = Colors.orange.withOpacity(0.5)
);
```

**13. 斜切画布**  
&emsp;&emsp;sx是水平方向的斜切，sy是垂直方向的斜切。  
&emsp;&emsp;斜切值是正弦tan值(对边除领边)，斜切45度：tan(pi / 4) = 1  
```dart
void skew(double sx, double sy)
```

```dart
canvas.drawRect(
    Rect.fromLTWH(60, 60, 140, 60),
    Paint()..color = Colors.orange.withOpacity(0.5)
);

//斜切45度
canvas.skew(1, 0);

canvas.drawRect(
    Rect.fromLTWH(60, 60, 140, 60),
    Paint()..color = Colors.orange.withOpacity(0.5)
);
```


**14. save()**  
&emsp;&emsp;```save()``` 用来保存```Canvas```的状态,```save()```方法之后的代码，可以调用```Canvas```的平移、放缩、旋转、裁剪等操作。  
&emsp;&emsp;```restore()```用来恢复```Canvas```之前保存的状态(可以想成是保存坐标轴的状态),防止```save()```方法代码之后对```Canvas```执行的操作，继续对后续的绘制会产生影响，通过该方法可以避免连带的影响。  
&emsp;&emsp;实际上，```save()```保存的就是```Canvas```中坐标轴的状态。  
&emsp;&emsp;注意，```save() ```并不会创建新的图层，和 ```saveLayer()``` 是不同的。  

&emsp;&emsp;平移画布后，先后绘制一大一小两个矩形，可见两个矩形左上角都在```(100,100)```这个坐标位置。  
```dart
canvas.translate(100, 100);
canvas.drawRect(
    Rect.fromLTWH(0, 0, 60, 60),
    Paint()..color = Colors.orange.withOpacity(0.5)
);

canvas.drawRect(
    Rect.fromLTWH(0, 0, 40, 40),
    Paint()..color = Colors.red.withOpacity(0.5)
);
```

&emsp;&emsp;先```save()```保存当前画布后，将画布平移后绘制第一个矩形，再```restore()```重置画布状态后绘制第二个矩形。  
```dart
canvas.save();
canvas.translate(100, 100);
canvas.drawRect(
    Rect.fromLTWH(0, 0, 60, 60),
    Paint()..color = Colors.orange.withOpacity(0.5)
);
canvas.restore();

canvas.drawRect(
    Rect.fromLTWH(0, 0, 40, 40),
    Paint()..color = Colors.red.withOpacity(0.5)
);
```

&emsp;&emsp;```save()```前，坐标轴原点是在```(0,0)```这个位置，调用```save()```后将坐标轴移动```(100, 100)```，此时原点即```(100,100)```,之后在画布上的操作都是以```(100, 100)```为原点所进行的。调用```restore()```之后，画布恢复到移动前的位置，此时画布上的操作以```(0,0)```为坐标原点。  

**15. saveLayer()**  
&emsp;&emsp;```saveLayer()``` 会创建一个新的图层。在``` saveLayer() ```到``` restore() ```之间的操作，是在新的图层上进行的。  
&emsp;&emsp;```saveLayer()```的第一个参数```rect```用来设置新图层的范围区域。绘制操作只有在这个区域内才会有效，超过这个区域的部分会被忽略。  
&emsp;&emsp;**每一个saveLayer()或者save()都必须有一个对应的restore()**  
&emsp;&emsp;**在传入的 Paint 必须设置过 color，否则设置的 rect 范围限制将会失效！**  

&emsp;&emsp;**saveLayer特别消耗性能，尽量少用**  

> &emsp;&emsp;saveLayer会在GPU中分配一块新的绘图缓冲区（离屏渲染），切换绘图目标，这些操作是在GPU中非常的耗时，尤其在比较老的设备上。  
> &emsp;&emsp;当Text的overflow属性为TextOverflow.fade，且文字超出范围时，会调用saveLayer。  
> &emsp;&emsp;使用Clip.antiAliasWithSaveLayer作为剪切行为时，会调用saveLayer（据说早期Flutter版本中大都使用这一方式）。建议优先使用Clip.hardEdge和Clip.antiAlias。这部分属性一般在ClipRect、ClipOval和ClipPath等裁剪功能Widget中用到。  
> &emsp;&emsp;修改RawChip的isEnabled属性，触发enableAnimation动画时，会调用saveLayer。  
> &emsp;&emsp;除过上面所说的显式调用耗时方法，还存在部分隐式调用的（Opacity、ShaderMask、ColorFilter、PhysicalModel、BackdropFilter等）。  



```dart
canvas.saveLayer(
  Rect.fromCircle(
    center: Offset(size.width / 2, size.height / 2),
    radius: 60
  ),
  _paint
);

//用画笔的颜色填满画布
canvas.drawPaint(Paint()..color = Colors.blue);

//在绘制区域以外绘制一个同样大小矩形
canvas.drawRect(Rect.fromLTWH(0, 0, size.width / 2, size.height / 2), Paint()..color = Colors.red);

canvas.restore();
```

&emsp;&emsp;如果 ```Paint ```没有设置混合参数，新图层就相当于仅仅是盖在了前面的图层之上。将``` Paint ```设置``` BlendMode``` 混合模式。新的图层将和之前的内容的像素进行了混合。  

```dart
///原底层设置为蓝色
canvas.drawRect(
  Rect.fromLTWH(0, 0, size.width, size.height),
  Paint()..color = Colors.lightBlueAccent
);

canvas.saveLayer(
    Rect.fromCircle(
      center: Offset(size.width / 2, size.height / 2),
      radius: 40
    ),
    Paint()..color = Colors.white
    ..blendMode = BlendMode.exclusion
);

///新图层填充绿色
canvas.drawPaint(Paint()..color = Colors.green);
///新图层绘制红色矩形
canvas.drawRect(Rect.fromLTWH(0, 0, size.width / 2, size.height / 2), Paint()..color = Colors.red);

canvas.restore();
```







