# 简介
- EagleBase使用Retrofit+rxjava进行网络请求，进行了二次封装。
- EagleBase已经引入如下相关依赖

	```groovy
    implementation 'com.squareup.okhttp3:okhttp:3.10.0'
    implementation 'com.squareup.okhttp3:logging-interceptor:3.9.0'
    implementation 'com.squareup.retrofit2:retrofit:2.4.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.3.0'
    implementation 'com.squareup.retrofit2:adapter-rxjava:2.3.0'
    implementation 'com.squareup.retrofit2:adapter-rxjava2:2.4.0'
    implementation 'com.squareup.retrofit2:converter-scalars:2.3.0'
    implementation 'io.reactivex.rxjava2:rxjava:2.1.5'
    implementation 'io.reactivex.rxjava2:rxandroid:2.0.1'
	```
- 核心类为ApiFactory，它是单例，createApi()会创建一个Retrofit对象，同时创建一个OkHttpClient对象。在OkHttpClient已包含网络超时时间设置、Https处理、Token拦截器TokenHeaderInterceptor、网络判断拦截器NetworkStateInterceptor、异常处理拦截器HttpInterceptor。还对有多种baseUrl的情况通过拦截器进行了处理，详细见下面举例：多种baseUrl的处理。

# 如何进行网络请求
## 普通的GET请求，返回String类型
注意createApi传参有"Constants.TYPE_STRING"。
- 首先定义service

	```java
    @GET("system/getCodes")
    Observable<String> getCode();
	```

- 然后在需要进行网络请求的类里，进行类似如下请求

	```java
	  Observable<String> userDateRangeString = ApiFactory.getInstance().createApi(MyRetrofitService.class, MineConstants.TYPE_STRING).getCode();
        ApiFactory.getInstance().setObservable(this, userDateRangeString,true,false, new RxCallback<String>() {

            @Override
            public void onNext(String s) {
                    ToastUtil.normal(s);
            }

            @Override
            public void onError(Throwable e) {
                ToastUtil.normal(e.getMessage());
            }

            @Override
            public void onCompleted() {

            }

        });
	```

## 普通GET请求，返回一个对象
- 首先定义service

	```java
	@Headers("url_name:biz")
    @GET("partners/{id}")
    Observable<HttpResult<FriendDetailModel>> friendDetail(@Path("id") String id); 
	```

- 然后在需要进行网络请求的类里，进行类似如下请求

	```java
	Observable<HttpResult<FriendDetailModel>> observable = ApiFactory.getInstance().createApi(UikitRetrofitService.class).friendDetail(pId);
    ApiFactory.getInstance().setObservable(this, observable, new RxCallback<HttpResult<FriendDetailModel>>() {
        @Override
        public void onNext(HttpResult<FriendDetailModel> result) {
            
        }

        @Override
        public void onError(Throwable e) {

        }

        @Override
        public void onCompleted() {

        }
    });
	```

- 下面是FriendDetailModel代码，省略了get/set方法

	```java
	public class FriendDetailModel extends BaseModel {
	    private String pId;
	    private String nickName;
	｝
	```

- 下面是HttpResult代码，省略了get/set方法

	```java
	public class HttpResult<T> {
	    private T data;
	    private int status;
	    private String message;
	}
	```

## 普通的POST请求,返回String类型
- 首先定义service

	```java
	public interface WalletRetrofitService {
   	 	//内网地址
   		 @POST("getIntranetWalletApi")
   		 Observable<String> getIntranetWalletApi();
	}
	```
- 然后在需要进行网络请求的类里，进行类似如下请求
	
	```java
	 Observable<String> observable = ApiFactory.getInstance().createApi(WalletRetrofitService.class, Constants.TYPE_STRING).getIntranetWalletApi();
  	ApiFactory.getInstance().setObservable(this, observable, false, true, new RxCallback<String>() {
            @Override
            public void onNext(String s) {
                Log.e(TAG, s);
                JSONObject jsonObject = null;
                try {
                    jsonObject = new JSONObject(s);
                    String data = jsonObject.getString("data");
                    if (data != null) {
                        if (!data.endsWith("/")) {
                            data = data + "/";
                        }
                        getUrlConfig().setBASE_URL(data);
                        ApiFactory.getInstance().apis.clear(); //清除之前缓存的api
                        hasBaseUrl = true;
                    }

                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }

            @Override
            public void onError(Throwable throwable) {

            }

            @Override
            public void onCompleted() {

            }
        });
	```
## 普通POST请求，返回一个对象

- 首先定义service

	```java
	public interface WalletRetrofitService {
		@POST("pmsapi/getOrderListNew")
	    Observable<HttpResult<List<StayListModel>>> postStayList(@Body StayListParam stayListParam); 
	}
	```

- 然后在需要进行网络请求的类里，进行如下请求

	```java
    Observable<HttpResult<List<StayListModel>>> observable = ApiFactory.getInstance().createApi(WalletRetrofitService.class).postStayList(stayListParam);
    ApiFactory.getInstance().setObservable(context, observable, new RxCallback<HttpResult<List<StayListModel>>>() {
        @Override
        public void onNext(HttpResult<List<StayListModel>> result) {
            walletCallback.onNext(result);
        }

        @Override
        public void onError(Throwable throwable) {
            walletCallback.onError(throwable);
        }

        @Override
        public void onCompleted() {
            walletCallback.onCompleted();
        }
    });
  
	```

- 下面是StayListParam，省略了get/set方法

	```java
	public class StayListParam extends BaseModel {
	    private String eid;
	    private String ethAddress;
	    private String orderStateNo;
	}
	```

- 下面是StayListModel，省略了get/set方法

	```java
	public class StayListModel extends BaseModel {
	    private String orderNo;
	    private String hotelId;
	    private String hotelName;
	}
	```

## 使用flatmap进行嵌套请求
- 参考如下

	```java
	DateRangeParam param = new DateRangeParam();
        param.setN(n);
        param.setCount(1000);
        Observable<MasterInfoModel> observable = ApiFactory.getInstance().createApi(RetrofitService.class)
                .getMasterDateRange(param)
                .flatMap(new Func1<List<DateRangeModel>, Observable<MasterInfoModel>>() {
                    @Override
                    public Observable<MasterInfoModel> call(List<DateRangeModel> dateRangeModels) {
                        MasterParam param = new MasterParam();
                        param.setN(n);
                        return ApiFactory.getInstance().createApi(RetrofitService.class).getMasterInfo(param);
                    });
        ApiFactory.getInstance().

                    setObservable(this,observable, new RxCallback<MasterInfoModel>() {
                        @Override
                        public void onNext (MasterInfoModel masterInfoModel){
                            Logger.e("onNext:" + masterInfoModel);
                        }

                        @Override
                        public void onError (Throwable throwable){

                        }

                        @Override
                        public void onCompleted () {

                        }
                    });
	```

## 下载文件
- 参考如下

	```java
	ApiFactory.getInstance().createApi(RetrofitService.class).download("yingyongbao")
	                        .enqueue(new RetrofitFileCallback<ResponseBody>() {
	                            @Override
	                            public void onSuccess(Call<ResponseBody> call, Response<ResponseBody> response) {
	                                ToastUtil.showShort(NetSampleActivity.this, "下载成功");
	
	                            }
	
	                            @Override
	                            public void onFailure(Call<ResponseBody> call, Throwable t) {
	                                Logger.e("onFailure");
	                            }
	
	                            @Override
	                            public void onLoading(long total, long progress) {
	                                super.onLoading(total, progress);
	                            }
	                        });
	```
## 上传文件
- 参考如下

	```java
	Map<String, Object> map = new HashMap<>();
	                map.put("address", "");
	                map.put("gender", "");
	                map.put("height", 42);
	                map.put("weight", 21);
	                map.put("realname", "fsd");
	                map.put("waist", 43);
	                map.put("userid", 285);
	                File file = new File("/sdcard/test.jpg");
	                RequestBody body1 = RequestBody.create(MediaType.parse("application/otcet-stream"), file);
	                RetrofitFileCallback<User> callback = new RetrofitFileCallback<User>() {
	                    @Override
	                    public void onSuccess(Call<User> call, Response<User> response) {
	                        Log.d("debug", "返回结果--》" + response.body().toString());
	                    }
	
	                    @Override
	                    public void onFailure(Call<User> call, Throwable t) {
	                        t.printStackTrace();
	                    }
	
	                    @Override
	                    public void onLoading(final long total, final long progress) {
	                        runOnUiThread(new Runnable() {
	                            @Override
	                            public void run() {
	                                if (total != progress) {
	//                                        mResultTextView.setText("总大小--》"+total+",,进度-->"+progress);
	                                } else {
	//                                        mResultTextView.setText("上传成功");
	                                }
	                            }
	                        });
	
	                    }
	                };
	                FileRequestBody body = new FileRequestBody(body1, callback);
	                MultipartBody.Part part = MultipartBody.Part.createFormData("avatarByte", file.getName(), body);
	                ApiFactory.getInstance().createApi(RetrofitService.class).uploadFile(part, map).enqueue(callback);
	
	```