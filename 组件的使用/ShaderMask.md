# ShaderMask
&emsp;&emsp;这是遮罩组件，可以使子组件拥有多种颜色或渐变效果。shader 是处于下层的，child 处于上层，也就是说，是 child 盖住了 shader。
可以通过 blendMode 来控制他俩重叠部分的效果。  
&emsp;&emsp;[BlendMode的详细介绍](https://blog.csdn.net/chenlove1/article/details/84574237)  
  
&emsp;&emsp;基本用法：  
&emsp;&emsp;**注意，把字体颜色设置为白色，不然效果不理想**

```dart
ShaderMask(
  //shaderCallback 需要返回一个Shader对象，可以通过渐变相关类直接生成Shader
  shaderCallback: (Rect bounds){
    //可用的Gradient有[LinearGradient], [RadialGradient], [SweepGradient]
    return RadialGradient(
      center: Alignment.center,
      radius: 1,
      tileMode: TileMode.mirror,
      colors: [Colors.yellowAccent,Colors.blue,Colors.greenAccent]
    ).createShader(bounds);
  },
  //blendMode属性是融合方式，child是目标图像(dst),Shader是源图像(src),
  //默认是modulate
  blendMode : BlendMode.modulate,
  child: Text(
    '这是一段测试文字',
    style:TextStyle(
        fontSize: ScreenUtil().setSp(36),
        color: Colors.white
    ),
  ),
)
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/20200512_134312_ShaderMask_01.jpg)

***
  
    
&emsp;&emsp;用于FlatButton：  
&emsp;&emsp;现在做一个按钮，要求按钮背景颜色渐变从上至下0xff2092e2至0xff07bcbe，按钮child是一个白色的Icon。

```dart
ShaderMask(
  shaderCallback: (Rect bounds){
    return LinearGradient(
        begin: Alignment.topCenter,
        end: Alignment.bottomCenter,
        tileMode: TileMode.mirror,
        colors: [Color(0xff2092e2),Color(0xff07bcbe)]
    ).createShader(bounds);
  },
  child: FlatButton(
    padding: EdgeInsets.all(0),
    onPressed: (){

    },
    child: Container(
      alignment: Alignment.center,
      width: ScreenUtil().setWidth(180),
      height: ScreenUtil().setHeight(68),
      child: Icon(
        Icons.mode_edit,
        color: Colors.white,
      ),
    ),
  ),
),
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/20200512_134312_ShaderMask_02.jpg)  

&emsp;&emsp;可见并不是如需求那样，因为blendMode属性不对，下面将blendMode属性修改为BlendMode.saturation，同时把FlatButton的color设置为透明色Colors.transparent。

```dart
ShaderMask(
  shaderCallback: (Rect bounds){
    return LinearGradient(
      begin: Alignment.topCenter,
      end: Alignment.bottomCenter, 
      tileMode: TileMode.mirror,
      colors: [Color(0xff2092e2),Color(0xff07bcbe)]
    ).createShader(bounds);
  },
  blendMode: BlendMode.saturation,
  child: FlatButton(
    padding: EdgeInsets.all(0),
    color: Colors.transparent,
    onPressed: (){

    },
    child: Container(
      alignment: Alignment.center,
      width: ScreenUtil().setWidth(180),
      height: ScreenUtil().setHeight(68),
      child: Icon(
        Icons.mode_edit,
        color: Colors.white,
      ),
    ),
  ),
),
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/20200512_134312_ShaderMask_03.jpg)

&emsp;&emsp;这样成功实现了需求中要求的样式。  
&emsp;&emsp;[源代码](lib/base_widget/base_widget_shadermask.dart)  




