# BackdropFilter
&emsp;&emsp;高斯模糊组件。  
&emsp;&emsp;BackdropFilter只处理父组件，不对子组件产生效果。  
* filter  
&emsp;&emsp;通过ImageFilter.blur来设置模糊度，一般在0.0~10.0之间，超过10完全不可见。  

* 实例
```dart
Column(
  mainAxisAlignment: MainAxisAlignment.center,
  children: <Widget>[
    Container(
      width: ScreenUtil().setWidth(600),
      height: ScreenUtil().setHeight(600),
      alignment: Alignment.center,
      decoration: BoxDecoration(
        image: DecorationImage(
          image: AssetImage(R.intPic.int_pic_01)
        ),
        color: Colors.red[100],
      ),
      child: BackdropFilter(
        filter: ImageFilter.blur(sigmaY: 10,sigmaX: 10),
        child: Container(
          width: 200,
          height: 200,
          color: Colors.greenAccent,
        ),
      ),
    ),
    Container(
      width: ScreenUtil().setWidth(400),
      height: ScreenUtil().setHeight(400),
      color: Colors.orange[100],
      child: Image(image: AssetImage(R.intPic.int_pic_01)),
    )
  ],
),
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/BackdropFilter/20200513_100156_BackdropFilter.jpg)  
