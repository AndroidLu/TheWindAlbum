# TheWindAlbum
### 零、功能介绍
仿微信样式的相册多选、单选；视频多选、单选；调用系统相机拍照
### 一、集成方式
#### 1、在项目的 build.gradle 添加如下代码
```
allprojects {
  repositories {
    maven { url 'https://jitpack.io' }
  }
}
```
#### 2、在 module 的 build.gradle 添加如下代码
```
implementation 'com.github.TheWindMeanFar:TheWindAlbum:1.1.2'
```
### 二、使用方式
#### 1、打开相机拍照

（1）使用默认配置
```
TheWind.get().with(MainActivity.this)
  .initValue()
  .openCamera(); // 打开相机拍照
```
（2）也可以自定义一些配置
```
TheWind.get().with(MainActivity.this)
  .initValue()
  .setCircle(true)  // 可选设置，裁剪时是否为圆形，默认为 false 矩形，如果拍照后不需要裁剪，可以不用设置
  .setCameraFileSavePath(savePathRoot + "camera_" + new Date().getTime() + ".jpg") // 可选设置，不设置的话会有默认路径
  .setCropFileSavePath(savePathRoot + "crop_" + new Date().getTime() + ".jpg") // 可选设置，不设置的话会有默认路径，，如果拍照后不需要裁剪，可以不用设置
  .setMaxHeight(1000) // 可选设置，设置截图后的图片最大高度，默认 0 表示不限制，如果拍照后不需要裁剪，可以不用设置
  .setMaxWidth(1000) // 可选设置，设置截图后的图片最大宽度，默认 0 表示不限制，如果拍照后不需要裁剪，可以不用设置
  .openCamera(); // 打开相机拍照
```
#### 2、打开相册选择图片

（1）使用默认配置
```
TheWind.get().with(MainActivity.this)
  .initValue()
  .openAlbum(); // 打开相册
```
（2）也可以自定义一些配置
```
TheWind.get().with(MainActivity.this)
  .initValue()
  .setCircle(true) // 可选设置，裁剪时是否为圆形，默认为 false 矩形，如果拍照后不需要裁剪，可以不用设置
  .setMaxSize(2) // 可选设置，设置最多能选几张图，默认 9 张
  .setMaxHeight(1000) // 可选设置，设置截图后的图片最大高度，默认 0 表示不限制
  .setMaxWidth(1000) // 可选设置，设置截图后的图片最大宽度，默认 0 表示不限制
  .setCompressFileSavePath(savePathRoot) // 可选设置，大于 300K 的图片会进行压缩，不设置的话会有默认路径
  .setCropFileSavePath(savePathRoot + "crop_" + new Date().getTime() + ".jpg") // 可选设置，裁剪后的保存路径，不设置的话会有默认路径，，如果拍照后不需要裁剪，可以不用设置
  .openAlbum(); // 打开相册
```
#### 3、打开视频选择
```
TheWind.get().with(MainActivity.this)
  .initValue()
  .setMaxSize(5) // 可选设置，设置最多能选几个视频，默认 9 个
  .openVideo();
```
### 三、接收选择结果
选择结果是在 onActivityResult 中接收的，代码如下，里面的「裁剪完成」是针对「调用拍照」完成后「调用裁剪」返回的。
如果是在相册中对图片编辑调用的裁剪，在 lib 中已经处理好了。
```
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        switch (requestCode) {
            // 视频选择完成
            case TheWind.VIDEO_REQUEST_CODE:
                if (resultCode == RESULT_OK && data != null) {
                    ArrayList<String> pathList = (ArrayList<String>) data.getSerializableExtra(TheWind.SELECT_RESULT_PATH_LIST);
                    tvPath.setText("视频选择完成：" + pathList.toString());
                }
                break;
                
            // 相册选择完成
            case TheWind.ALBUM_REQUEST_CODE:
                if (resultCode == RESULT_OK && data != null) {
                    ArrayList<String> pathList = (ArrayList<String>) data.getSerializableExtra(TheWind.SELECT_RESULT_PATH_LIST);
                    tvPath.setText("相册选择完成：" + pathList.toString());
                }
                break;
                
            // 拍照完成
            case TheWind.CAMERA_REQUEST_CODE:
                if (resultCode == RESULT_OK) {
                    if (new File(TheWind.get().getCameraFileSavePath()).exists()) {
                        // 如果不裁剪，这就是拍照后的路径
                        // tvPath.setText("拍照成功：" + TheWind.get().getCameraFileSavePath());
                        // 如果要裁剪，注意裁剪不需要调用 initValue()
                        TheWind.get().with(MainActivity.this).openCrop();
                    } else tvPath.setText("拍照失败");
                }
                break;
                
            // 裁剪完成（拍照完成后调用裁剪触发的）
            case TheWind.CROP_REQUEST_CODE:
                if (resultCode == RESULT_OK) {
                    if (new File(TheWind.get().getCropFileSavePath()).exists()) {
                        tvPath.setText("裁剪成功：" + TheWind.get().getCropFileSavePath());
                    } else
                        tvPath.setText("裁剪失败");
                }
                break;
        }
    }
```
