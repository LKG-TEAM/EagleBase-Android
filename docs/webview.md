# 关于WebView
EagleBase里对WebView作了大量的封装以应对各种使用WebView的情况。比如，在WebView页面点击一个按钮，可能要跳转到另一个WebView，也可能需要在当前页面跳转。又比如，某个WebView主导的app，在浏览器里运行会有一套代码，在手机上运行，可能会尽可能调用原生来实现actionbar、网络请求、跳转等等。而且原生与WebView的交互方面，EagleBase提供了交互的桥梁，并定义了规范，您必须阅读此文档才能了解这些内容。

# 怎么用？
## 跳转到一个普通的WebViewActivity。
- 可以直接通过跳转一个url地址来路由到EagleWebViewActivity。
	
	```java
	// 在本地asset里的www.bclwallet文件夹下index.html
	String url = "file:///android_asset/www-bclwallet/index.html";
	navigateTo(url);
	```
	```java
	//跳转到网页
	String url = "http://www.baidu.com";
	navigateTo(url);
	```
	```java
	//跳转网页，携带参数，参数拼接在url后面，参数会在新打开的页面的Bundle对象里。
	String url = "http://www.baidu.com?param1=value1&param2=value2";
	navigateTo(url);
	```

## 自定义WebViewActivity
- 自定义类`CustomEagleWebViewActivity`

	```java
	public class CustomEagleWebViewActivity extends EagleWebViewActivity {}
	```
- 在`CustomEagleWebViewActivity`里面重写`getWebJavaInterfaces`方法

	```java
		@Override
    public Object getWebJavaInterfaces() {
        hideActionBar();
        return new CustomerJavaInterfaces();
    }
	```

- 自定义类`CustomerJavaInterfaces`，里面根据以下格式写自己定义的用来给js调用的原生方法。

	```java
	 public class CustomerJavaInterfaces {

        /**
         * 获取deviceId，
         */
        @JavaInterface4JS("getDeviceId")
        public void getDeviceId(@Param("callbackId") final String callbackId) {
            String deviceId = BLEUtils.getDeviceId(CustomEagleWebViewActivity.this);
            syncInvokeJsCallback(callbackId, deviceId, false);
        }

		/**
         * 获取BaseUrl，网络请求，耗时
         */
        @JavaInterface4JS("getWalletApiUrl")
        public void getWalletApiUrl(@Param("callbackId") final String callbackId) {
            String url = BaseApplication.getDefault().getUrlConfig().getBASE_URL();
            syncInvokeJsCallback(callbackId, url, false);
        }

	    /**
	     * 同步调用jsCallback
	     *
	     * @param callbackId
	     * @param callbackContent
	     */
	    public void syncInvokeJsCallback(String callbackId, String callbackContent, boolean isStatus) {
	        if (!TextUtils.isEmpty(callbackId) && !TextUtils.isEmpty(callbackContent)) {//有这个callbackId则通知js
	            String jsonWrapper;
	            if (!isStatus) {//传输数据
	                jsonWrapper = "{\"data\":\"" + callbackContent + "\"}";//似乎格式都是这样
	            } else {//通知状态
	                jsonWrapper = "{\"status\":\"" + callbackContent + "\"}";//似乎格式都是这样
	            }
	
	            Log.e("JsCallback", callbackId + "-->invokeJsCallback raw json:" + jsonWrapper);
	            String base64Dat = Base64.encodeToString(jsonWrapper.getBytes(), Base64.DEFAULT);
	            base64Dat = base64Dat.replace("\n", "");
	            fetchSuccessCallback(base64Dat, callbackId, true);
	        }
	    }

	    public void fetchSuccessCallback(@Param("resp") String resp, @Param("callbackId") String fetchCallbackId, @Param("isBase64") Boolean isBase64) {
	        if (this.getComponent() != null) {
	            this.getComponent().getInvokeJS().fetchSuccessCallback(resp, fetchCallbackId, isBase64);
	        }
	
	    }
	｝

	```

## WebModule子类
- 如果是Web类型模块，入口是`WebModule`的子类，那么直接重写`WebModule`的方法`getWebJavaInterfaces()`。

	```java
    /**
     * 获取自定义
     *
     * @return
     */
    @Override
    public Object getWebJavaInterfaces() {
        return new CustomerJavaInterfaces();
    }
	```
- 然后`CustomerJavaInterfaces`类的定义同上。

## 原生调用JS方法
- `WebViewComponent`里提供了方法可以方便地调用JS方法，如果是在Activity里，可以通过`getComponent()`拿到`WebViewComponent`对象。

	```
	String json = new Gson().toJson(event);
    json = Base64.encodeToString(json.getBytes(), Base64.DEFAULT);
    json = json.replace("\n", "");
    getInvokeJS().invokeJSMethod("getSignature", json);
	```

## `WebViewComponent`介绍。
- `WebViewComponent`是所有WebView页面的核心组件，包含的内容有：
	+ `setCustomerJavaInterfaces()`将自定义的JavaInterface注册到全局。
	+ `getInvokeJS()`获取`IBridgeInvoke `对象。
	+ 下拉刷新，以及是否需要下拉刷新。
	+ WebView的初始化和基本Setting。
	+ 初始化`initConsoleJs()`，用来显示vConsole。
	+ 网络请求、打开相机、打开相册等等常规方法的封装。
	+ 返回按键的处理。
	+ WebView加载失败的处理。
	+ `getEagleBridge（）`获取`SimpleJavaJsBridge`对象。
	+ `getWebView（）`获取WebView对象。
	+ `reloadWebView()`重新加载WebView。