## 项目app的入口规定
> 我们规定应用首页分带Tab(tab中间带加号与不带加号)、带侧滑Menu、单页(Single)几种。对应的入口类分别是BaseTabActivity的子类MainTabActivity、BaseMenuActivity的子类MainMenuActivity、ModuleContainer。
	
## 各个业务模块的入口规定
> 我们规定所有业务模块的首页必须是NativeModule或WebModule的子类，装载进ModuleContainer。业务模块首页由平台自动生成，比如我们业务模块名称是EagleGesturePassword，那么它的首页是EagleGesturePasswordNativeModule。

## 关于应用的配置'app.json'
- 我们在移动平台上生成一个应用后，会自动创建`app.json`这样一个配置文件。
	+ 'app.json'可能内容如下，如下是一个tab类型的native app，有4个tab，分别配置在urls里。
	
		```json
		{
	  "nativeapp":{
	    "urls":[
	      "/crm/testcrmtwo/testcrmtwowebmodule?icon=\ue699&title=我的&color=#222222&selectColor=#2097f4",
	      "/eagleneteaseim/eagleneteaseimnativemodule?icon=\ue699&title=你的&color=#222222&selectColor=#2097f4",
	      "/crm/testcrmtwo/testcrmtwowebmodule?icon=\ue699&title=我的&color=#222222&selectColor=#2097f4",
	      "/ib/ib/ibwebmodule?icon=\ue699&title=你的&color=#222222&selectColor=#2097f4"
	
	    ],
	    "type":0,
	  "webapp":null,
	  "configurations":{
	    "bugly":{
	      "appId":"544b983bbc"
	    },
	    "shareSDK":{
	      "umeng":"5ad04358f29d984ab7000078",
	      "platforms":{
	        "weibo":{
	          "appKey":"3921700954",
	          "appSecret":"04b48b094faeb16683c32669824ebdad",
	          "redirectURL":"https://sns.whalecloud.com/sina2/callback"
	        },
	        "wechat":{
	          "appKey":"wxdc1e388c3822c80b",
	          "appSecret":"3baf1193c85774b3fd9d18447d76cab0",
	          "redirectURL":"http://mobile.umeng.com/social"
	        },
	        "tencent":{
	          "appKey":"1105821097",
	          "appSecret":"",
	          "redirectURL":"http://mobile.umeng.com/social"
	        }
	      }
	    },
	    "guide":[
	      "闪屏1.png",
	      "闪屏2.png",
	      "闪屏3.png",
	      "闪屏4.png"
	    ],
	    "login":{
	      "loginUrl": "/lmsplogin/lmsploginnativemodule",
	      "isLoginAlways": true,
	      "loginIcon":"loginIcon.png",
	      "loginCopyRight":"string",
	      "loginTitle":"loginTitle",
	      "api":{
	        "env":"dev",
	        "dev": {
	          "login":"http://192.168.10.165:8091/m/MLogin",
	          "verify":"http://192.168.10.165:8091/rand?rc="
	        },
	        "product": {
	          "login":"https://ibank.mszq.com:443/ibf/MLoginServlet",
	          "verify":"https://ibank.mszq.com:443/ibf/rand?rc="
	        }
	      }
	    }
	  }
	}
		```

	+ `app.json`也可能如下，下面是一个单个页面的native app，urls里是这个页面的路由地址。
		
		```json
		{
	  "nativeapp": {
	    "urls": [
	        "/bclwallet/bclwalletwebmodule?icon=&title=&color=#323232&selectColor=#2097f4" 
	      ],
	    
	    "extra": {
	        "type": "add",
	        "style": {
	            "subType": "round",
	            "title": "",
	            "backgroundColor": "",
	            "foregroundColor": "",
	            "image": ""
	         },
	        "extraParams": [
	            
	        ]},
	    
	    "type":-1
	  },
	  "webapp": null,
	  "configurations": {
	        "login": null
	                
	  }
	}
	
		```

- 在BaseApplication里，对这个文件进行了解析。方法为`initAppConfig()`，并且提供了相关的get方法。我们可以在项目里获取相关的配置信息。
- BaseApplication里主要对外开放的方法：
	+ 获取Application对象
		
		```
		getDefault()
		```

	+ 获取UrlConfig
	
		```java
		getUrlConfig()
		```

	+ 如果是Web类型app，获取WebUrl
	
		```
		getWebUrl()
		```

	+ 获取app类型。配置是tab还是menu，由后台提供，勿需改动,0tab,1menu，2带加号的tab，必须在应用设置AppConfig，否则报空指针

		```
		getEagleType()
		```

	+ 配置Login的数据，由后台配置，勿需改动，如果返回是null，则没有login

		```
		getLoginConfigBean()
		```

	+ 返回入口Activity路由地址字符串

		```
		getEntryActivity()
		```

	+ 获取Tab类型app的ExtraStyle。

		```
		getTabbarExtraStyle()
		```

	