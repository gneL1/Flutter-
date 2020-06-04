# Shape的使用
&emsp;&emsp;自定义一个```shape```，继承自```ShapeBorder```。  
&emsp;&emsp;定义两个方法类型将```getOuterPath```和```paint```方法暴露出去。  

```dart
typedef ShapePaint<Canvas,Size,TextDirection> =
void Function(Canvas canvas, Rect rect,TextDirection textDirection);

typedef ShapeOuterPath<Rect , TextDirection > =
Path Function(Rect rect, TextDirection textDirection);

class MyBorder extends ShapeBorder{


  final ShapePaint<Canvas,Rect,TextDirection> shapePaint;
  final ShapeOuterPath<Rect,TextDirection> shapeOuterPath;

  const MyBorder({
    this.shapePaint,
    this.shapeOuterPath
  });

  @override
  // TODO: implement dimensions
  EdgeInsetsGeometry get dimensions => throw UnimplementedError();

  @override
  Path getInnerPath(Rect rect, {TextDirection textDirection}) {
    // TODO: implement getInnerPath
//    throw UnimplementedError();
    return null;
  }

  ///返回一个Path对象，也就是形状的裁剪
  @override
  Path getOuterPath(Rect rect, {TextDirection textDirection}) {
    // TODO: implement getOuterPath
//    throw UnimplementedError();
    return shapeOuterPath(rect,textDirection);
//    return null;
  }

  ///rect是组件的区域
  @override
  void paint(Canvas canvas, Rect rect, {TextDirection textDirection}) {
    // TODO: implement paint
    shapePaint(canvas,rect,textDirection);
  }

  @override
  ShapeBorder scale(double t) {
    // TODO: implement scale
//    throw UnimplementedError();
    return null;
  }

}

```

&emsp;&emsp;```paint```方法用来在组件上绘制图像。  
```dart
void paint(Canvas canvas, Rect rect, {TextDirection textDirection})
```

&emsp;&emsp;```getOuterPath```方法要求返回一个```Path```，这个```Path```就是组件的形状。   
```dart
Path getOuterPath(Rect rect, {TextDirection textDirection})
```

## 画个圈圈图案再设置个圆角矩形
```dart
shape: MyBorder(
  ///绘制图像
  shapePaint: (canvas, rect, textDirection) {
    canvas.drawCircle(
        Offset(rect.height * 0.25, rect.height * 0.25),
        rect.height * 0.15,
        Paint()..color = Colors.white..style = PaintingStyle.stroke..strokeWidth = 1
    );

    canvas.drawCircle(
        Offset(rect.height * 0.25, rect.height * 0.25),
        rect.height * 0.08,
        Paint()..color = Colors.black
    );
  },
  ///修改形状
  shapeOuterPath: (rect, textDirection) {
    Path _path = Path();
    _path.addRRect(
      RRect.fromRectAndRadius(rect, Radius.circular(10))
    );
    return _path;
  },
)
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Shape/shape_01.jpg)

## 打洞
```dart
shape: MyBorder(
  shapeOuterPath: (rect, textDirection) {
    Path _path = Path();
    ///evenOdd意味着第奇数个图像会和相邻的第偶数个图像，重叠部分被去除
    _path.fillType = PathFillType.evenOdd;
    var _h = rect.height;
    var _w = rect.width;
    _path.addRRect(
      RRect.fromRectAndRadius(rect, Radius.circular(5))
    );

    var _distance = _h * 0.15;
    var _space = _h * 0.15;
    while(_distance < _w){
      _path.addOval(
          Rect.fromCircle(
              center: Offset(_distance, _h * 0.15),
              radius: _h * 0.1
          )
      );

      _distance = _distance + _space + _h * 0.15;
    }

    return _path;
  },
  shapePaint: (canvas, rect, textDirection) {

  },
)
```
![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%A7%86%E5%9B%BE/photos/Shape/shape_02.jpg)
