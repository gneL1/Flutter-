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


