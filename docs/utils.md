# PermissionUtil 处理6.0权限
因为考虑到拒绝权限后的处理，所以要求传拒绝权限后弹出的dialog显示的字符串。
考虑到8.0不支持自动申请权限组，所以单独申请read和write权限,以后可能要封装权限组的管理类。

	```java
  	if (PermissionUtil.checkPermissions(activity, 0, new PermissionUtil.PermissionDeniedMsg() {
        @Override
        public String setDenieDialog() {
            /**
             * param 拒绝权限后的dialog显示的内容
             比如，可以是"您没有授予相关权限"
             */
            return dinieDialogMsg;
        }
    }, PERMS_READ_EXTERNAL, PERMS_WRITE_EXTERNAL, PERMS_CAMERA)) {
       //获取权限后要做的代码
    }
	```

# FragmentUtil Fragment管理工具
通过navigateTo获取fragment对象，可以传入参数bundle。通过FragmentUtils.goToFragment()实现跳转到该fragment。如果该fragment没有add，则会add，如果已经add了，则会show。

	```java
    mUnpaidFragment = navigateTo(BCLWalletConstants.ROUTER.BCLWALLETMODULE_BLUETOOTHUNPAIDFRAGMENT,bundle);
	FragmentUtils.goToFragment(this, mUnpaidFragment, R.id.framelayout);

	``

# AssetUtil 对asset进行操作的工具，获取asset里的内容。
获取一个对象或者一个String，调用getData()方法，例如：

	```java
	MenusModel menusModel = AssetsUtil.getData(MenusModel.class,this,"mock/menus.json");
	```
获取一个Bitmap对象，调用getBitmapFromAssetsFile()方法

	```java
	getBitmapFromAssetsFile();
	```

# LightStatusBarUtil 设置状态栏深色或浅色
在BaseActivity初始化时有对状态栏深浅的设置，如下

	```java
	//状态栏深浅
       int statusBarLightOrDark = BaseApplication.getDefault().getStatusBarLightOrDark();
       LightStatusBarUtil.setLightStatusBar(this, statusBarLightOrDark == 0 ? true : false);
	```

# NetworkUtil 网络环境的判断

我们通常是收到网络改变的广播后，判断当前是什么网络，然后来做一些操作。示例代码如下：
- 在activity里注册广播

	```java
    private NetworkChangedReceiver mNetworkChangedReceiver;
  	/**监听网络状态改变的广播**/
    mNetworkChangedReceiver = new NetworkChangedReceiver();
    IntentFilter intentFilter = new IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION);
    getActivity().registerReceiver(mNetworkChangedReceiver, intentFilter);

	```
- 广播的接收
	
	```java
	   /**
     * 注册监听网络状态改变的广播
     */
    public class NetworkChangedReceiver extends BroadcastReceiver {

        private boolean mNetWorkOff;

        @Override
        public void onReceive(Context context, Intent intent) {
            int netWorkStates = NetworkUtil.getNetWorkType(context);

            if (netWorkStates == NetworkUtil.NETWORK_TYPE_INVALID) {
                Log.e(TAG, "网络断开");
                mNetWorkOff = true;
                //断网了

            } else if (netWorkStates == NetworkUtil.NETWORK_TYPE_WAP || netWorkStates == NetworkUtil.NETWORK_TYPE_2G || netWorkStates == NetworkUtil.NETWORK_TYPE_3G || netWorkStates == NetworkUtil.NETWORK_TYPE_WIFI) {
                Log.e(TAG, "网络连接");
                if (mNetWorkOff && !ModuleManager.getInstance().isHasBaseUrl()) {
                    ModuleManager.getInstance().doNetToSetBizUrl();
                    String json = "{\"status\":\"success\",\"msg\":\"yes\"}";
                    json = Base64.encodeToString(json.getBytes(), Base64.DEFAULT);
                    json = json.replace("\n", "");
                    getInvokeJS().invokeJSMethod("onNetworkChanged", json);
                }
            }
        }
    }

	```

- 页面销毁时销毁广播

	```
    broadcastManager.unregisterReceiver(mReceiver);

	```

# ScreenUtil 屏幕工具类

- 获取屏幕宽度
	
	```
	getScreenWidth()
	```

- 获取屏幕高度
	
	```
	getScreenHeight()
	```
- 根据手机的分辨率从 dp 的单位 转成为 px(像素)
	
	```
	dip2px()
	```

- 根据手机的分辨率从 px(像素) 的单位 转成为 dp
	
	```
	px2dip()
	```
- 获取状态栏高度
	
	```
	getStatusBarHeight()
	```

- 获取导航栏高度
	
	```
	getNavigationBarHeight()
	```

# SharePreUtil  配置文件工具类

- 从配置文件读取字符串，其它类型基本数据一样。
	
	```
    String token = SharePreUtil.getString(Constants.TOKEN, BaseApplication.getDefault(), Constants.TOKEN);

	```

- 从配置文件获取对象
	
	```
    mLastConnectedRoom = SharePreUtil.getObject("lastConnectedRoom", this, "lastConnectedRoom", StayListModel.class);

	```
- 存储字符串到配置文件，其它类型基本数据一样。
	
	```
    SharePreUtil.putString(TAG, context, DEVICE_ID, deviceID);

	```

- 存放对象
	
	```
    SharePreUtil.putObject("lastConnectedRoom", this,
                                    "lastConnectedRoom", StayListModel.class, sCurrentSelectedRoom);
	```

# StatusBarUtil  StatusBar工具类
- 设置状态栏颜色4.4-5.0

	```
	setStatusBarColor(Activity activity, int statusColor)
	```

- 5.0以上修改状态栏颜色

	```
	setHighStatusBarColor(Activity activity, int statusColor)
	```

- 无actionBar时默认黑色状态栏

	```
	setBaseStatusBarColor(Activity activity)
	```


- 设置自定义statusbar颜色

	```
	setCustomStatusBarColor(Activity activity, int color)
	```

# SystemUtil android系统工具类
- 获取包名
	
	```
	getPackageName(Context context)
	```

- 获取应用名
	
	```
	getAppName(Context context)
	```

- 返回版本名称
	
	```
	getVersionName(Context context)
	```

- 返回版本号
	
	```
	getVersionCode(Context context)
	```

- 获取Android系统版本号比如6.0
	
	```
	getSystemVersion()
	```

- 获取设备ID
	
	```
	getDeviceId(Context context)
	```

- 获取状态栏高度
	
	```
	getStatuBarHeight()
	```

- 获取当前手机系统语言。
	
	```
	getSystemLanguage()
	```

# ToastUtil 吐司工具

	```
    ToastUtil.normal("再按一次退出");
    ToastUtil.error("该路由地址不属于Module的地址");
	```

# UrlUtil Url工具
- 获取协议
	
	```
    String protocol = UrlUtil.getProtocol(url);

	```

- 获取参数
	
	```
	bundle = UrlUtil.getParams(bundle, url);//把url后面的参数放进bundle

	```

- 获取路径

	```
    String path = UrlUtil.getPath(url);//path a/b

	```

# XDecoderUtil 加密解密，进制转换等工具
- DES算法，加密

	```
	encode(String key, String data)
	```
- DES算法，解密

	```
	decode(String key, String data)
	```

- 二行制转字符串

	```
	```

- MD5 加密

	```
	encodeMD5(String str)
	```