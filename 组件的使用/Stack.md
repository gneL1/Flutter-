# Stack
&emsp;&emsp;层叠组件  
属性|描述
:-|:-|
AlignmentGeometry alignment = AlignmentDirectional.topStart|对齐方向
TextDirection textDirection|排列方向
StackFit fit = StackFit.loose|确定如何适应大小
Overflow overflow = Overflow.clip|决定如何显示超出Stack显示空间的子组件

&emsp;&emsp;排列方向有```TextDirection.rtl```,从右往左。```TextDirection.ltr```，从左往右。  
&emsp;&emsp;```StackFit.loose```表示子组件自己决定，```StackFit.expand```表示尽可能的大，```StackFit.passthrough```根据```Stack```的
父布局来调整没有定位的子布局大小。  
&emsp;&emsp;如果子组件超过```Stack```边界由```overflow```控制，值为```Overflow.clip```时，
超出部分会被剪裁（隐藏），值为```Overflow.visible```时则不会。  

&emsp;&emsp;基本使用：  
```dart
Stack(
  overflow: Overflow.visible,
  textDirection: TextDirection.rtl,
  children: <Widget>[
    Container(
      width: ScreenUtil().setWidth(600),
      height: ScreenUtil().setHeight(200),
      color: Colors.red[100].withOpacity(0.6),
    ),

    Container(
      width: ScreenUtil().setWidth(400),
      height: ScreenUtil().setHeight(60),
      color: Colors.greenAccent[100].withOpacity(0.6),
    ),

    Container(
      width: ScreenUtil().setWidth(100),
      height: ScreenUtil().setHeight(150),
      color: Colors.blue[100].withOpacity(0.6),
    ),
  ],
)
```

![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/Stack/Stack.jpg)

***

# IndexedStack
&emsp;&emsp;```Stack```的子类，通过参数```index```显示指定的子组件。

```dart
int _index = 0;

IndexedStack(
  index: _index,
  textDirection: TextDirection.rtl,
  children: <Widget>[
    Container(
      width: ScreenUtil().setWidth(600),
      height: ScreenUtil().setHeight(200),
      color: Colors.red[100].withOpacity(0.6),
    ),



    Container(
      width: ScreenUtil().setWidth(400),
      height: ScreenUtil().setHeight(60),
      color: Colors.greenAccent[100].withOpacity(0.6),
    ),

    Container(
      width: ScreenUtil().setWidth(100),
      height: ScreenUtil().setHeight(150),
      color: Colors.blue[100].withOpacity(0.6),
    ),
  ],
)

FlatButton(
  onPressed: (){
    _index++;
    if(_index > 2){
      _index = 0;
    }
    setState(() {

    });
  },
  child: Text('切换'),
)

```

![图片示例](https://github.com/gneL1/Flutter-/blob/master/%E7%BB%84%E4%BB%B6%E7%9A%84%E4%BD%BF%E7%94%A8/photos/Stack/IndexedStack.gif)

