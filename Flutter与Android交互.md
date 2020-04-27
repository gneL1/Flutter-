# Flutter端
&emsp;&emsp;Flutter包含三个功能，分别是获取电池电量，不带参数的跳转到Android界面，带参数的跳转到Android界面
```dart

//通过MethodChannel调用连接Android的通道
//单个应用中使用的通道名称必须唯一
static const platform = const MethodChannel('samples.flutter.io/battery');
  
String _batteryLevel = 'Unknown battery level';
String oneAct = '';
String twoAct = '';
	
//获取电池电量
Future<Null> _getBatteryLevel()async{
  //调用可能失败，所以放在try-catch里
  try{
    final int result = await platform.invokeMethod('getBatteryLevel');
    _batteryLevel = 'Battery level at $result %';
  }on PlatformException catch(e){
    _batteryLevel = "Failed to get battery level : '${e.message}";
  }
  setState(() {
		
  });
}
	
//跳转页面不带参数
Future<Null> _jumpToNative() async{
	try{
    oneAct = await platform.invokeMethod('oneAct');
  }on PlatformException catch(e){
    oneAct = "Failed to jump to OneActivity : '${e.message}";
  }
  setState(() {
     
  });
}
	
//跳转页面带参数
Future<Null> _jumpToNativeWithValue() async{
	try{
    Map<String,String> map = {"flutter" : "这是来自flutter的参数"};
   	twoAct = await platform.invokeMethod('twoAct',map);
  }on PlatformException catch(e){
    twoAct = "Failed to jump to TwoActivity : '${e.message}";
  }
  setState(() {
		
  });
}
	
```

***

# Android端
* 点击File -- Open -- 点开当前项目 -- 选中android -- OK
- Activity的创建三要素
	1. 新建一个类继承Activity或AppCompatActivity
	2. 在AndroidMainfest中声明(不声明的话无法使用无法跳转)
	3. 创建layout并在Activity中的onCreate里通过setContentView绑定
	
> 添加需要导入的依赖
	
```dart
import android.content.ContextWrapper;
import android.content.Intent;
import android.content.IntentFilter;
import android.os.BatteryManager;
import android.os.Build.VERSION;
import android.os.Build.VERSION_CODES;
import android.os.Bundle;
```
	
```dart
public class MainActivity extends FlutterActivity  {
	//通道名称,和Flutter端一致
	private static final String CHANNEL = "samples.flutter.io/battery";
		
	@Override
  public void onCreate(Bundle savedInstanceState) {
  	super.onCreate(savedInstanceState);
		new MethodChannel(getFlutterView(), CHANNEL).setMethodCallHandler(
    	new MethodChannel.MethodCallHandler() {
      	@Override
      	public void onMethodCall(MethodCall call, MethodChannel.Result result) {
					//获取电池电量
        	if (call.method.equals("getBatteryLevel")) {
          	int batteryLevel = getBatteryLevel();
						if (batteryLevel != -1) {
					  	result.success(batteryLevel);
            } else {
              result.error("UNAVAILABLE", "Battery level not available.", null);
            }
           }
					 
					 //跳转到指定Activity不带参数
           else if(call.method.equals("oneAct")){
            Intent intent = new Intent(MainActivity.this,MyFlutterActivity.class);
            startActivity(intent);
						//返回给flutter的参数
            result.success("跳转成功flutter");
           }
					 
						//跳转到指定Activity带参数
          	else if(call.method.equals("twoAct")){
           	//解析参数
            String text = call.argument("flutter");
            Intent intent = new Intent(MainActivity.this,OtherActivity.class);
            intent.putExtra(OtherActivity.VALUE,text);
            startActivity(intent);
					 	result.success("跳转成功");
           }
					else {
          	result.notImplemented();
          }
         }
       });
		}
		
		//获取电池电量
		public int getBatteryLevel() {
        int batteryLevel = -1;
        if (VERSION.SDK_INT >= VERSION_CODES.LOLLIPOP) {
            BatteryManager batteryManager = (BatteryManager) getSystemService(BATTERY_SERVICE);
            batteryLevel = batteryManager.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY);
        } else {
            Intent intent = new ContextWrapper(getApplicationContext()).
                    registerReceiver(null, new IntentFilter(Intent.ACTION_BATTERY_CHANGED));
            batteryLevel = (intent.getIntExtra(BatteryManager.EXTRA_LEVEL, -1) * 100) /
                    intent.getIntExtra(BatteryManager.EXTRA_SCALE, -1);
        }

        return batteryLevel;
    }
		
```

11111

```dart
public class MainActivity extends FlutterActivity  {
    //通过MethodChannel调用连接Android的通道，确保通道名称与Flutter端一致
    private static final String CHANNEL = "samples.flutter.io/battery";

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        new MethodChannel(getFlutterView(), CHANNEL).setMethodCallHandler(
                new MethodChannel.MethodCallHandler() {
                    @Override
                    public void onMethodCall(MethodCall call, MethodChannel.Result result) {
										    //电池电量
                        if (call.method.equals("getBatteryLevel")) {
                            int batteryLevel = getBatteryLevel();
                            if (batteryLevel != -1) {
                                String delete = "待删除";
                                result.success(batteryLevel);
                            } else {
                                result.error("UNAVAILABLE", "Battery level not available.", null);
                            }
                        }
                        else if(call.method.equals("oneAct")){
                            //跳转到指定Activity
                            Intent intent = new Intent(MainActivity.this,MyFlutterActivity.class);
                            startActivity(intent);
//
//                            //返回给flutter的参数
                            result.success("跳转成功flutter");
                        }
                        else if(call.method.equals("twoAct")){
                            //解析参数
                            String text = call.argument("flutter");

                            //带参数跳转到指定Activity
                            Intent intent = new Intent(MainActivity.this,OtherActivity.class);
                            intent.putExtra(OtherActivity.VALUE,text);
                            startActivity(intent);

                            result.success("跳转成功");
                        }

                        else {
                            result.notImplemented();
                        }
                    }
                });
    }


//    private static void registerCustomPlugin(PluginRegistry registry){
//        FlutterPluginJumpToAct.registerWith(registry.registrarFor(FlutterPluginJumpToAct.CHANNEL));
////        FlutterPluginJumpToAct.registerWith(registry.registrarFor(FlutterPluginCounter.CHANNEL));
//    }




    public int getBatteryLevel() {
        int batteryLevel = -1;
        if (VERSION.SDK_INT >= VERSION_CODES.LOLLIPOP) {
            BatteryManager batteryManager = (BatteryManager) getSystemService(BATTERY_SERVICE);
            batteryLevel = batteryManager.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY);
        } else {
            Intent intent = new ContextWrapper(getApplicationContext()).
                    registerReceiver(null, new IntentFilter(Intent.ACTION_BATTERY_CHANGED));
            batteryLevel = (intent.getIntExtra(BatteryManager.EXTRA_LEVEL, -1) * 100) /
                    intent.getIntExtra(BatteryManager.EXTRA_SCALE, -1);
        }

        return batteryLevel;
    }


    /**
     * 请求权限
     */
    private void initPermissions() {
        permissionsList.clear();

        //判断哪些权限未授予
        for(String permission : permissions){
            if(ActivityCompat.checkSelfPermission(this,permission)!= PackageManager.PERMISSION_GRANTED){
                permissionsList.add(permission);
            }
        }

        //请求权限
        if(!permissionsList.isEmpty()){
            String[] permissions = permissionsList.toArray(new String[permissionsList.size()]);//将List转为数组
            ActivityCompat.requestPermissions(MainActivity.this, permissions, PERMISSION_REQUEST);
        }
    }


    /**
     * 权限回调,
     * @param requestCode
     * @param permissions
     * @param grantResults
     */
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);

        switch (requestCode){
            case PERMISSION_REQUEST:
                break;
            default:
                break;
        }
    }


}
```
	
	
	
	
