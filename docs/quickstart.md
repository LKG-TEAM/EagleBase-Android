# 快速开始


## About EagleBase

EagleBase是基础模块，所有模块和应用都依赖此模块。

* 目录结构。核心类目录结构如下

```
-com/linkstec/eagle/base
					    -core
						-component
						-http
						-adapter
						-update
						-web
						-utils
						-module
```

* core目录包含：Activity的基础类BaseActivity、Fragment的基础类BaseFragment、组件Component的基础类BaseComponent、应用基础类
	BaseApplication、Stack管理类ActivityStack、Tab和Menu两种基本首页基础类BaseTabActivity和BaseMenuActivity等。

* component目录：包含WebViewComponent，所有web类型页面都必须装载此组件实现web的加载。

* http目录：包含网络请求的相关类，入口是ApiFactory或Api。

* adapter目录：包含RecyclerView的相关基础适配器，通用适配器是BasicRecycleViewAdapter，还包含带粘滞头部的适配器SectionedRecyclerAdapter。

* update目录：包含版本更新相关的类，入口是UpdateChecker。

* web目录：包含h5与原生交互相关的类。其中EagleWebViewActivity是默认所有web打开的入口类，内部装载有一个WebViewComponent。
BridgeJavaInterfaces里有定义EagleBase默认的可以直接h5调用原生的方法。

* utils目录：

	- ActionBarUtil -actionbar的相关操作。
	- AssetUtil -读取assets的操作。
	- FileUtil -文件相关操作，读写删除等。
	- FragmentUtil -add或显示隐藏Fragment。
	- LightStatusBarUtil -浅色状态栏工具。
	- MediaHelper -相机、相册、裁剪的操作。
	- NetworkUtil -网络检测工具。
	- PathUtils -项目路径管理工具。
	- PermissionUtil -权限管理工具。
	- ScreenUtil -屏幕工具类。
	- SettingsHolder -针对setting.json的操作实现对应用的配置。
	- SharePreUtil -SharePreference工具。
	- StatusBarUtil -StatusBar工具。
	- StringUtil -String工具。
	- SystemUtil -系统相关工具。获取包名、版本号等。
	- ToastUtil -土司工具。
	- UrlUtil -Url工具。获取url的协议、参数等。
	- XDecoderUtil -DES加密解密工具。

* module目录：包含ModuleContainer、NativeModule、WebModule。ModuleContainer继承自BaseActivity，是所有模块的入口类的容器，里面装载一个NativeModule或者WebModule。NativeModule是所有Native模块的入口类的父类，继承自BaseFragment。WebModule是所有Web模块的入口类的父类。

## ProGuard
不需要配置

## Add EagleBase to your project
相关配置(这些配置由移动平台完成，不需手动更改)
* 在project根目录的build.gradle里

```groovy
    repositories {
        jcenter()
        maven { url 'https://maven.google.com' }
        maven { url "http://192.168.2.94:10080/repertory/maven/dependency/raw/master/" }
    }

   dependencies {
        classpath 'com.android.tools.build:gradle:3.1.4'
    }
```

* 在project根目录的config.gradle里


```groovy
ext {
......
    configuration = [
         package            : "com.linkstec.eagle.base",
         compileVersion     : 27,
         minSdk             : 17,
         targetSdk          : 27,
         version_code       : 1,
         version_name       : "1.0.2",
         dev_base_url       : "\"null\"",
         release_base_url   : "\"null\""
    ]

    libraries = [
            supportVersion: "27.1.1"
    ]
......
}
```

* 在module的build.gradle里

```groovy
compile 'com.linkstec.eagle.base:EagleBase:1.0.2
```

其中1.0.2换成最新的版本，或者使用1.+的形式。
