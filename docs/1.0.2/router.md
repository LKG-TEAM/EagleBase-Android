# 路由

## 路由介绍
- 路由使用的是阿里的Arouter。地址为：https://github.com/alibaba/ARouter
- 为了避免在使用阿里路由的时候遇到坑，这里介绍一下该库的集成方法。

- 在所有module的build.gradle里配置以下内容，这里用annotationProcessor不用apt，因为apt会导致databinding失效：

```groovy
apply plugin: 'com.neenbedankt.android-apt'
    android {
       ...
        defaultConfig {
            javaCompileOptions {
                annotationProcessorOptions {
                    arguments = [moduleName: project.getName()]
                    includeCompileClasspath false
                }
            }
        }
    }
dependencies {
   ...
    annotationProcessor 'com.alibaba:arouter-compiler:1.1.4'
}
```

## 如何使用

- 在要使用路由的Activity或Fragment里加上注解

	```java
	@Route(path = "/eagle/net/NetSampleActivity")
	public class NetSampleActivity extends BaseActivity {
	}
	
	@Route(path = "/eagle/fragment/HomeFragment")
	public class HomeFragment extends BaseFragment {
	}
	```

- 注解的格式约定
	1. 所有的路由地址都不用大写字母，用小写。
	2. 本地Activity或Fragment，必须以斜杠"/"开头。

	```java
	//	/项目名称/文件夹名/文件名 ,如果没有文件夹名则不写
	@Route(path = "/bclwallet/bclwalletwebmodule")
	```

	3. path内字符串一般写在自定义的Constant类里。

- 发起路由

1. 跳转到普通Activity:

	```java
	navigateTo(RouterConstants.NET_SAMPLE_ACTIVITY);
	```
	
RouterConstants.NET_SAMPLE_ACTIVITY里定义的字符串为"/eaglehostsample/net/netsampleactivity"，而在类NetSampleActivity已经定义了该类的路由地址：@Route(path = "/eaglehostsample/net/netsampleactivity")

2. 跳转到普通Activity并携带参数和请求码：

	```java
	navigateTo(RouterConstants.NET_SAMPLE_ACTIVITY, RouterConstants.RequestCode.NET_SAMPLE, bundle);
	```

3. 获取某个Fragment，然后加载它：

	```java
	Fragment fragment = navigateTo(RouterConstants.FRAGMENT_HOME_FRAGMENT);
	if (fragment != null) {
	    getSupportFragmentManager().beginTransaction().add(R.id.container, fragment).commitAllowingStateLoss();
	}
	```
            
4. 跳转到相机：
	
	```java
	Bundle extras = new Bundle();
	extras.putString(Constants.DENIED_MSG, "您没有开启相机权限!");
	navigateTo(RouterConstants.MEDIA_OPEN_CAMERA, Constants.RequestCode.REQ_CAMERA, extras);
	```
      
5. 跳转到相册：
	
	```java
	Bundle albumBundle = new Bundle();
	albumBundle.putString(Constants.DENIED_MSG, "您没有开启文件读写权限");
	navigateTo(RouterConstants.MEDIA_OPEN_ALBUM, Constants.RequestCode.REQ_ALBUM, albumBundle);
	```

6. 在onActivityResult里拿到拍照的结果后跳转到裁剪：

	```java
	String path = data.getData().getPath();
	//  mIv.setImageURI(uri);
	Bundle cropBundle = new Bundle();
	cropBundle.putString(Constants.CROP_PATH, path);
	cropBundle.putInt(Constants.CROP_OUT_X, 500);
	cropBundle.putInt(Constants.CROP_OUT_Y, 250);
	cropBundle.putInt(Constants.CROP_ASPECT_X, 2);
	cropBundle.putInt(Constants.CROP_ASPECT_Y, 1);
	navigateTo(RouterConstants.MEDIA_OPEN_CROP, Constants.RequestCode.REQ_CROP, cropBundle);
	```

7. 跳转到扫描二维码(需要依赖EagleQRCode模块)：
	
	```java
	 this.navigateTo("eagle://eaglemedia/qrscanner", Constants.RequestCode.REQ_QRCODE);
	
	```

8. 跳转到生成二维码(需要依赖EagleQRCode模块)：

	```java
	Bundle bundle = new Bundle();
	bundle.putString("data", data);
	bundle.putBoolean("useDefaultIcon", useDefaultIcon);
	bundle.putString("iconUrl", iconUrl);
	this.navigateTo("eagle://eaglemedia/qrgenerate", bundle);
	```

9. H5跳转到原生Activity，调用openWindow方法，传入路由地址，需要以协议eagle://开头

	```java
	eagle://eaglehostsample/navigate/navigatesampleactivity
	```

10. 跳转到http://www.baidu.com，实际上是新开一个页面，跳转到EagleWebViewActivity，直接传网页地址即可

	```java
	navigateTo("http://www.baidu.com")
	```