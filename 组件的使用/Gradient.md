# LinearGradient
&emsp;&emsp;线性渐变
## 属性
属性|描述
:-|:-|
begin|渐变开始的位置(Alignment)
end|渐变结束的位置(Alignment)
colors|渐变的颜色(List<Color>)
stops|渐变分割，每段渐变所占的百分比，长度和colors一致(List<double>)
tileMode|平铺模式(TileMode)

* TileMode 平铺，即如何重复渐变色：
   1. TileMode.clamp - 默认，没效果
   
   ```dart
   gradient: LinearGradient(
      begin: Alignment.centerLeft,
      end: Alignment.center,
      colors: [Colors.lightBlueAccent,Colors.orange,Colors.greenAccent],
      tileMode: TileMode.clamp,
    ),
   ```
   
   ![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/gradient/20200512_145227_gradient_01.jpg)
   
   
   2. TileMode.mirror - 镜面反射
   
   ```dart
   gradient: LinearGradient(
      begin: Alignment.centerLeft,
      end: Alignment.center,
      colors: [Colors.lightBlueAccent,Colors.orange,Colors.greenAccent],
      tileMode: TileMode.mirror,
      stops: [0.2,0.3,1]
    ),
   ```
   
   ![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/gradient/20200512_145227_gradient_02.jpg)
   
   
   3. TileMode.repeated - 重复
   
   ```dart
   gradient: LinearGradient(
      begin: Alignment.centerLeft,
      end: Alignment.center,
      colors: [Colors.lightBlueAccent,Colors.orange,Colors.greenAccent],
      tileMode: TileMode.repeated,
    ),
   ```
   
   ![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/gradient/20200512_145227_gradient_03.jpg)
   
   ***
   
* stops 渐变分割  
&emsp;&emsp; stops控制每段颜色所占的百分比，个数必须和 colors 中颜色值个数匹配，要不显示不出来。
stops 里的数值综合最好 = 1，否则显示有偏差，stops 里的数值必须是升序的,要不一样显示不出来。

```dart
_item(
  text: 'stops',
  gradient: LinearGradient(
    stops: [0.15,0.35,0.5],
    colors: [Colors.lightBlueAccent,Colors.orange,Colors.greenAccent],
  ),
)
```
   
![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/gradient/20200512_145227_gradient_04.jpg)

***

* 给文字创建渐变效果

```dart
Text(
  '这是一段渐变的文字',
  style: TextStyle(
    fontSize: ScreenUtil().setSp(24),
    //根据gradient创建shader,
    //createShader的参数rect指定了渐变的范围
    foreground: Paint()..shader = LinearGradient(
      colors: [Colors.red,Colors.blue,Colors.orange]
    ).createShader(Rect.fromLTWH(0, 0, 100, 10))
  ),
),
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/gradient/20200512_145227_gradient_05.jpg)

***

* 做一段会闪烁的文字  
colors中间的颜色是要闪烁的颜色，两边的颜色是原本的颜色  
**这里特别注意，controller的释放要防止super.dispose()之前，不然会报错**
   
```dart
AnimationController _animationController;
AnimationController get animationController => _animationController;
Animation<double> _animation;
Animation<double> get animation => _animation;

@override
void initState() {
  // TODO: implement initState
  _animationController = AnimationController(
      vsync: this,
      duration: Duration(milliseconds: 1000)
  );
  _animation = Tween(
    begin: 0.0,
    end: 1.0,
  ).animate(_animationController);
  _animationController.repeat();
}

@override
void dispose() {
  // TODO: implement dispose
  //_animationController.dispose()的位置应该在super.dispose()前执行。
  //先调用controller.dispose释放了动画资源，再调用super
  _animationController.stop();
  _animationController.dispose();
  super.dispose();
//    _animationController.stop();
//    _animationController.dispose();

}

AnimatedBuilder(
  animation: _animation,
  builder: (context,child){
    return Text(
      '这是一段会闪烁的文字',
      style: TextStyle(
          fontSize: ScreenUtil().setSp(24),
          foreground: Paint()..shader = LinearGradient(
            colors: [Colors.orange,Colors.blue,Colors.orange],
            stops: [_animation.value - 0.2,_animation.value,_animation.value + 0.2]
          ).createShader(Rect.fromLTWH(0, 0, 200, 10))
      ),
    );
  },
),
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/gradient/20200512_145227_gradient_06.jpg)

***

# RadialGradient
&emsp;&emsp;径向渐变
## 属性
属性|描述
:-|:-|
center|渐变中心(Alignment)
radius|渐变半径(double)
colors|渐变的颜色(List<Color>)
stops|渐变分割，每段渐变所占的百分比，长度和colors一致(List<double>)
tileMode|平铺模式(TileMode)

* 使用实例
```dart
gradient: RadialGradient(
  center: const Alignment(0.7, -0.6), // near the top right
  radius: 0.2,
  colors: [
  const Color(0xFFFFFF00), // yellow sun
  const Color(0xFF0099FF), // blue sky
  ],
  stops: [0.4, 1.0],
),

gradient: RadialGradient(
  center: Alignment.center,
  radius: 0.4,
  colors: [
    const Color(0xFFFFFF00), 
    const Color(0xFF0099FF), 
  ],
)
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/gradient/20200512_145227_gradient_07.jpg)

***

# SweepGradient
&emsp;&emsp;扫描渐变
## 属性  
属性|描述
:-|:-|
center|渐变中心(Alignment)
colors|渐变的颜色(List<Color>)
stops|渐变分割，每段渐变所占的百分比，长度和colors一致(List<double>)
tileMode|平铺模式(TileMode)
startAngle|渐变开始的角度,默认值0.0(double),
endAngle|渐变结束的角度,默认值pi*2(double)
transform|旋转起点(GradientTransform)

* center  
&emsp;&emsp; 渐变的中心点，范围是(-1.0,-1.0)~(1.0,1.0),即左上角至右下角。
center: Alignment.center即为设置中心点在正中心(0,0)。  

* startAngle & endAngle  
&emsp;&emsp; startAngle是第一个颜色开始渐变至第二个颜色的弧度，endAngle是渐变至最后一个颜色的弧度。
第一个颜色起点固定在右边中心。如果tileMode为默认值，则startAngle之前都是第一个颜色，endAngle之后都是第二个颜色。  

* stops  
&emsp;&emsp; 设置从startAngle至endAngle之间每段颜色所占的百分比。数组总和为1且递增。  

* 实例

```dart
gradient: SweepGradient(
  colors: [
    Colors.blue,
    Colors.orange,
    Colors.greenAccent
  ],
  center: Alignment.center,
  startAngle: pi / 2,
  endAngle: pi + pi / 2,
  stops: [0.1,0.2,0.7],
),
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/gradient/20200512_145227_gradient_08.jpg)

* transform  
&emsp;&emsp; 可以将固定在三点钟方向的起始位置旋转，transform类型是抽象类GradientTransform，这里使用它的子类GradientRotation来实现旋转。

* 实例

```dart
gradient: SweepGradient(
  colors: [
    Colors.blue,
    Colors.orange,
    Colors.greenAccent
  ],
  center: Alignment.center,
  startAngle: pi / 2,
  endAngle: pi + pi / 2,
  stops: [0.1,0.2,0.7],
  //顺时针旋转90度
  transform:GradientRotation(pi / 2)
),
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/gradient/20200512_145227_gradient_09.jpg)

