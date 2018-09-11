# 如何使用版本更新
- 在移动平台上设置如下格式的json，其中versionUpdate和updateUrl是规定的必传参数，必须有这两个参数才能进行版本更新。其他字段是根据后台需要传的参数自己定义的。比如下面的json具体就是，后台接口名称为app/version，此处如果不写baseUrl就会使用app的baseUrl，请求contentType和请求方式(POST/GET)跟updateUrl同级别，最好写上，如果不写就默认是application/json和POST，参数是用户自己定义的key，这里我们写的key是"params"，里面的"os"和"v"是后台需要传的参数。

	```json
	{
  	"versionUpdate":{
    "updateUrl":"app/version",
    "contentType":"application/json",
    "requestType":"post",
    "params":{
      "os":"android",
      "v":"v1.0.2"
	    }
	  }
	}
	```

- 在需要版本更新的地方加入如下代码

	```java
	 JSONObject updateObject = SettingsHolder.getInstance().getSettingValue("settings/versionUpdate", "/");
    if (updateObject != null) {
        UpdateChecker.checkForDialog(getActivity(), updateObject, new UpdateInterface() {
            @Override
            public void updateCallback(String response) {
                parseJson(response);
            }
        });
    }
	```

- 在parseJson(response)方法里拿到了后台返回的结果字符串，我们可以自己实现它来进行版本更新结果的处理，这里有例子：

	```java
	/**
     * @param result 结果字段如下
     *               upd 是修改还是新增功能，Y是，N否
     *               v 版本号
     *               url 下载链接
     *               log 更新日志
     *               md5 Md5值
     *               delta 是否全量
     *               size 大小，单位是byte
     *               must 是否强制更新 false否 true是
     *               格式：{  "code" : "E0005",  "msg" : "参数错误",  "data" : null}
     */
    private void parseJson(String result) {
        try {
            if (TextUtils.isEmpty(result)) {
                return;
            }
            JSONObject obj = new JSONObject(result);
            String code = obj.getString("code");
            if ("0".equals(code)) {
                JSONObject data = obj.getJSONObject("data");
                String updateMessage = (String) data.get("log");
                String apkUrl = (String) data.get("url");
	//                String apkCode = (String) data.get("v");
	//                int versionCode = AppUtils.getVersionCode(getActivity());
                if (data == null) {
                    Toast.makeText(getActivity(), "没有新的更新", Toast.LENGTH_SHORT).show();
                } else {

	//                if (mType == Constants.TYPE_NOTIFICATION) {
	//                    showNotification(mContext, updateMessage, apkUrl);
	//                } else if (mType == Constants.TYPE_DIALOG) {
                    showDialog(getActivity(), data);
	//                }
                }
            } else {
                ToastUtil.normal(obj.getString("msg"));
            }
        } catch (JSONException e) {
            Log.e(TAG, "check update: parse json error");
        }
    }
	```

- 弹出dialog的逻辑如下，其中UpdateDialog是EagleBase有的类，可以直接使用或者作为参考，您可以自定义一个类来实现自己UI等。

	```java
	/**
     * Show dialog
     */
    private void showDialog(Context context, JSONObject data) {
        UpdateDialog.show(context, data);
    }
	```

