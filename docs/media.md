## 打开相机

首先通过如下代码实现跳转到相机。

```java
Bundle extras = new Bundle();
extras.putString(Constants.DENIED_MSG, "您没有开启相机权限!");
navigateTo(Constants.ROUTER.OPEN_CAMERA, Constants.RequestCode.REQ_CAMERA, extras);
```
然后在onActivityResult里，写返回结果的处理。

```java
  @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == Constants.RequestCode.REQ_CAMERA) {
            if (new File(MediaHelper.getCameraPath()).exists()) {
                String path = MediaHelper.getCameraPath();
//                mIv.setImageURI(uri);
                Bitmap bitmap = BitmapFactory.decodeFile(MediaHelper.getCameraPath());
                mIv.setImageBitmap(bitmap);
            }
        }
    }
```

## 打开相册

首先通过如下代码实现跳转到相册。

```java
Bundle albumBundle = new Bundle();
albumBundle.putString(Constants.DENIED_MSG, "您没有开启文件读写权限");
navigateTo(Constants.ROUTER.OPEN_ALBUM, Constants.RequestCode.REQ_ALBUM, albumBundle);
```

然后在onActivityResult里，写返回结果的处理。

```java
  @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == Constants.RequestCode.REQ_ALBUM) {
            if (data != null) {

                String path = data.getData().getPath();
//                mIv.setImageURI(uri);
                Bundle cropBundle = new Bundle();
                cropBundle.putString(Constants.CROP_PATH, path);
                cropBundle.putInt(Constants.CROP_OUT_X, 500);
                cropBundle.putInt(Constants.CROP_OUT_Y, 250);
                cropBundle.putInt(Constants.CROP_ASPECT_X, 2);
                cropBundle.putInt(Constants.CROP_ASPECT_Y, 1);
                navigateTo(Constants.ROUTER.OPEN_CROP, Constants.RequestCode.REQ_CROP, cropBundle);
            }
        }
    }
```

## 打开裁剪
首先，通过如下方法跳转到裁剪。我们一般是在照相完毕或者选择图片完毕后进行裁剪，所以下面的方法经常写在打开相机或打开相册的onActivityResult里，如上面的打开相册，便是在返回结果里打开裁剪。

```java
Bundle cropBundle = new Bundle();
cropBundle.putString(Constants.CROP_PATH, path);
cropBundle.putInt(Constants.CROP_OUT_X, 250);
cropBundle.putInt(Constants.CROP_OUT_Y, 250);
cropBundle.putInt(Constants.CROP_ASPECT_X, 1);
cropBundle.putInt(Constants.CROP_ASPECT_Y, 1);
navigateTo(Constants.ROUTER.OPEN_CROP, Constants.RequestCode.REQ_CROP, cropBundle);
```

然后在onActivityResult里，写返回结果的处理。

```java
  @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == Constants.RequestCode.REQ_CROP) {
            if (new File(MediaHelper.getCropPath()).exists()) {
                Bitmap bitmap = BitmapFactory.decodeFile(MediaHelper.getCropPath());
                mIv.setImageBitmap(bitmap);
            }
        }
    }
```
