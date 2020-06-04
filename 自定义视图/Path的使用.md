# Path
&emsp;&emsp;```Path```的默认起始位置是在(0,0)。  

## 1. lineTo()
&emsp;&emsp;从起点绘制一条直线到lineTo指定的点。  
&emsp;&emsp;绘制完成后将lineTo指定的终点设置为下一次绘制的起点。  
```dart
void lineTo(double x, double y) 
```

```dart
Path _path = Path();
///把绘制的起点移动到屏幕中心
///moveTo本质上是开始一个新的子路径
_path.moveTo(size.width / 2, size.height / 2);

_path.lineTo(size.width, size.height / 2);
_path.lineTo(size.width / 4, size.height / 4 * 3);
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/lineTo.jpg)

## 2. quadraticBezierTo()
&emsp;&emsp;绘制二阶贝塞尔曲线。  
&emsp;&emsp;贝塞尔曲线需要三个点，一个起点，一个控制点，一个终点。  
```dart
void quadraticBezierTo(double x1, double y1, double x2, double y2)
```

```dart
_path.moveTo(0, size.height / 4);

///前两个参数是控制点的(x,y)
///后两个参数是终点的(x,y)
_path.quadraticBezierTo(
  size.width / 2, size.height,
  size.width, size.height / 2
);
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/quadraticBezierTo.jpg)

## 3. cubicTo()
&emsp;&emsp;三阶贝塞尔,需要两个控制点。  
```dart
void cubicTo(double x1, double y1, double x2, double y2, double x3, double y3)
```

```dart
_path.moveTo(0, size.height / 4);

///前两个参数是第一个控制点的(x,y)
///中间两个参数是第二个控制点的(x,y)
///后两个参数是终点的(x,y)
_path.cubicTo(
    size.width / 2, size.height,
    size.width / 3, 0,
    size.width, size.height / 2
);
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/cubicTo.jpg)

## 4. conicTo()
&emsp;&emsp;二次曲线，主要受weight参数的控制。  
&emsp;&emsp;权重大于1  绘制的是双曲线。  
&emsp;&emsp;权重等于1  绘制的是抛物线。  
&emsp;&emsp;权重小于1  绘制的是椭圆。  
```dart
void conicTo(double x1, double y1, double x2, double y2, double w) 
```

```dart
_path.moveTo(0, size.height / 4);

///void conicTo(double x1, double y1, double x2, double y2, double w)
///x1和y1是控制点(x1,y1)
///x2和y2是终点(x2,y2)
_path.conicTo(size.width / 2 , size.height, size.width, size.height / 2, num);
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/conicTo.gif)

## 5. arcTo()
&emsp;&emsp;弧线。  
属性|描述
:-|:-|
rect|圆弧所在的矩形
startAngle|开始的弧度,默认起点是在右边中心
sweepAngle|需要绘制的弧度大小,注意如果弧度大小是pi * 2 的倍数，那么弧度将不可见
forceMoveTo:|false时添加一条直线段和一条弧度；true时启动一个新的子路径，其中包含一个弧度
```dart
void arcTo(Rect rect, double startAngle, double sweepAngle, bool forceMoveTo)
```
&emsp;&emsp;```forceMoveTo```为```true```：  
```dart
Rect _rect = Rect.fromCircle(center: Offset(size.width / 2, size.height / 2),radius: 100);
_path.moveTo(0, size.height / 4);
_path.arcTo(
  _rect,
  0,
  pi,
  true
);
canvas.drawRect(_rect,Paint()..color = Colors.grey..strokeWidth = 1..style = PaintingStyle.stroke);
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/arcTo_01.jpg)

&emsp;&emsp;```forceMoveTo```为```false```：  
```dart
Rect _rect = Rect.fromCircle(center: Offset(size.width / 2, size.height / 2),radius: 100);
_path.moveTo(0, size.height / 4);
_path.arcTo(
  _rect,
  0,
  pi,
  false
);
canvas.drawRect(_rect,Paint()..color = Colors.grey..strokeWidth = 1..style = PaintingStyle.stroke);
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/arcTo_02.jpg)

## 6. addRect()
&emsp;&emsp;矩形。  
```dart
void addRect(Rect rect)
```

```dart
Rect _rect = Rect.fromCircle(center: Offset(size.width / 2, size.height / 2),radius: 80);
_path.addRect(_rect);
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/addRect.jpg)

## 7. addOval()
&emsp;&emsp;椭圆。  
```dart
void addOval(Rect oval)
```

```dart
Rect _rect = Rect.fromLTWH(size.width / 4, size.height / 4, 100, 40);
_path.addOval(_rect);
canvas.drawPath(_path, _paint);
canvas.drawRect(_rect, Paint()..color = Colors.grey .. strokeWidth = 1..style = PaintingStyle.stroke);               
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/addOval.jpg)

## 8. addArc()
&emsp;&emsp;弧线。  
```dart
void addArc(Rect oval, double startAngle, double sweepAngle)
```

```dart
Rect _rect = Rect.fromLTWH(size.width / 4, size.height / 4, 100, 40);
_path.addArc(_rect, pi / 6 , pi);
canvas.drawPath(_path, _paint);
canvas.drawRect(_rect, Paint()..color = Colors.grey .. strokeWidth = 1..style = PaintingStyle.stroke);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/addArc.jpg)

## 9. addPolygon()
&emsp;&emsp;多边形。  
&emsp;&emsp;```close```为```true```时，最后一个点和第一个点连接，多边形闭合。```false```时，不会连接。  
&emsp;&emsp;可以通过设置画笔```Paint```的```style```属性来设置是否用颜色填充多边形。  
&emsp;&emsp;```paint.style = PaintingStyle.fill```  
```dart
void addPolygon(List<Offset> points, bool close)
```

&emsp;&emsp;```close```为```true```：  
```dart
_path.addPolygon(
  [
    Offset.zero,
    Offset(size.width / 4, size.height),
    Offset(size.width / 4 * 3 , 0),
    Offset(size.width, size.height / 2)
  ],
  true
);
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/addPolygon_01.jpg)

&emsp;&emsp;```close```为```false```：  
```dart
_path.addPolygon(
    [
      Offset.zero,
      Offset(size.width / 4, size.height),
      Offset(size.width / 4 * 3 , 0),
      Offset(size.width, size.height / 2)
    ],
    false
);
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/addPolygon_02.jpg)

## 10. addRRect()
&emsp;&emsp;圆角矩形。  
```dart
void addRRect(RRect rrect)
```

```dart
RRect _rrect = RRect.fromRectAndRadius(
  Rect.fromCircle(
    center: Offset(size.width / 2, size.height / 2),
    radius: 60
  ),
  Radius.circular(10)
);

_path.addRRect(_rrect);
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/addRRect.jpg)

## 11. 绘制虚线
&emsp;&emsp;```PathMetric``` 是一个对 ```Path``` 进行测量并且能够提取子 ```Path``` 的工具。  
&emsp;&emsp;通过 ```path.computeMetrics()``` 获取到``` PathMetrics ```对象。
通过对 ```PathMetrics ```遍历，我们可以提取到子 ```Path```的```pathMetric```,再通过```extractPath()```方法截取出新的```path```作为虚线的每一小段。
```dart
///移动起点到左边中心
_path.moveTo(0, size.height / 2);

///连接右边中心
_path.lineTo(size.width, size.height / 2);

Path _dashPath = Path();
double dashWidth = 10.0;
double dashSpace = 5.0;
double distance = 0.0;

///forceClosed 表示是否要连接path的起始点
///computeMetrics()返回的是一个PathMetric集合PathMetrics
for(PathMetric pathMetric in _path.computeMetrics()){
  ///pathMetric.length就是path的长度
 while(distance < pathMetric.length){
    _dashPath.addPath(
      ///extractPath(double start, double end, {bool startWithMoveTo = true})
      ///将pathMetric按照给定的位置区间截取，然后返回这段path
      ///startWithMoveTo为true时，以path的起点为起点，为false时则以Offset(0,0)即画布的左上角为起点
      ///如果start >= stop,则返回null;
      pathMetric.extractPath(distance , distance + dashWidth,startWithMoveTo: true),
      Offset(0,0)
    );
    distance += dashWidth;
    distance += dashSpace;
  }
}

canvas.drawPath(_dashPath, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/dash.jpg)

## 12. close()
&emsp;&emsp;闭合路径。  
```dart
///开始一个新路径
_path.moveTo(0 , size.height / 2);
_path.lineTo(size.width / 2, size.height / 4 );
_path.lineTo(size.width / 2, size.height / 4 * 3);

///开始一个新路径
_path.moveTo(size.width, 0);
_path.lineTo(size.width / 4 * 3, size.height / 5 * 4);
_path.lineTo(size.width / 5 * 4, size.height / 2);
///闭合
_path.close();

canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/close.jpg)

## 13. addPath()
&emsp;&emsp;添加路径。  
```dart
void addPath(Path path, Offset offset, {Float64List matrix4})
```

```dart
Path _otherPath = new Path();
_otherPath.moveTo(0, 0);
_otherPath.lineTo(100, 100);
canvas.drawPath(_otherPath, Paint()..color = Colors.blue..strokeWidth = 4..style = PaintingStyle.stroke);

_path.addPath(_otherPath,Offset(size.width / 2,size.height / 2));
///清空路径
//_path.reset();
canvas.drawPath(_path, _paint);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/addPath.jpg)

## 14. fillType
&emsp;&emsp;路径填充。  
&emsp;&emsp;```fillType = PathFillType.evenOdd;```：  
```dart
_path.fillType = PathFillType.evenOdd;

_path.addRect(
  Rect.fromLTRB(40, 100, 200, 140),
);

_path.addRect(
  Rect.fromLTRB(100, 40, 140, 200),
);

canvas.drawPath(
    _path,
    Paint()..color = Colors.orange..style = PaintingStyle.fill..strokeWidth = 2
);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/evenOdd.jpg)

&emsp;&emsp;```fillType = PathFillType.nonZero;```：  
```dart
_path.fillType = PathFillType.nonZero;

_path.addRect(
  Rect.fromLTRB(40, 100, 200, 140),
);

_path.addRect(
  Rect.fromLTRB(100, 40, 140, 200),
);

canvas.drawPath(
    _path,
    Paint()..color = Colors.orange..style = PaintingStyle.fill..strokeWidth = 2
);
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Path/nonZero.jpg)




