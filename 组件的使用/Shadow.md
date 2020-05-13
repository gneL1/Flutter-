# BoxShadow
&emsp;&emsp;矩形阴影
## 属性
属性|描述
:-|:-
Color color = const Color(0xFF000000)|阴影默认颜色
Offset offset = Offset.zero|延伸的阴影，向右下偏移的距离
double blurRadius = 0.0|延伸距离,会有模糊效果
this.spreadRadius = 0.0|延伸距离,不会有模糊效果

* blurRadius  
```dart
boxShadow: [
  BoxShadow(
    color: Colors.orange[100],
    offset: Offset(10, 10),
    blurRadius: 10
  )
]
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/Shadow/20200513_103315_shadow_01.jpg)

***

* spreadRadius  
```dart
boxShadow: [
  BoxShadow(
      color: Colors.orange[100],
      offset: Offset(10, 10),
      spreadRadius: 10
  )
]
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/Shadow/20200513_103315_shadow_02.jpg)


***

* 设置多个阴影  
```dart
boxShadow: [
  BoxShadow(
      color: Colors.orange[100],
      offset: Offset(10, 10),
      blurRadius: 10
  ),

  BoxShadow(
      color: Colors.orange[100],
      offset: Offset(-10, -10),
      blurRadius: 10
  ),

  BoxShadow(
      color: Colors.orange[100],
      offset: Offset(10, -10),
      blurRadius: 10
  ),

  BoxShadow(
      color: Colors.orange[100],
      offset: Offset(-10, 10),
      blurRadius: 10
  ),
]
```

![运行结果](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/Shadow/20200513_103315_shadow_03.jpg)



