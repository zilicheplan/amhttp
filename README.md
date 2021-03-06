## 版本更新日志

### 版本1.0.7

增加日志开关，默认关闭。格式化返回的JSON数据。方便调试。

### 版本1.0.6

支持同步异步请求。修复一些已知问题。

### 版本1.0.5

支持文件上传。


## 用法

在project/module的build.gradle中添加：

```gradle
compile 'io.chelizi:amhttp:1.0.7'
```

## 服务端测试环境
git地址：https://github.com/Eddieyuan123/amhttp-server.git
这个项目是用来测试amhttp的，可以clone下来测试。（服务端代码采用springboot + springMVC + gradle）。

### 1.AMQuery 查询（get）

构建一个AMQuery对象，不需要关心内部如何实现，配置相应的参数即可。对于response返回的数据经Gson映射成实体。使用起来简单方便。支持同步异步请求。

```java
 AMQuery<Blog> query = new AMQuery<>();
        query.setUrl("http://192.168.1.10:8090/blog/id?id=1")
             .setCacheControl(CacheControl.FORCE_NETWORK)
             .setCallMethod(CallMethod.SYNC)//同步
             .openDebug(true)//日志开关
             .setTag(hashCode());
        query.findObjects(this, new OnFindListener<Blog>() {
            @Override
            public void onResponseSuccess(Blog response) {
                Toast.makeText(MainActivity.this,response.getTitle(),Toast.LENGTH_LONG).show();
            }

            @Override
            public void onResponseError(int code, @Nullable HttpError httpError) {

            }

            @Override
            public void onFailure(Exception e) {

            }
        });
```

### 2. AMPost 提交

构建一个AMPost对象。有两种提交参数的方法，1.addWhereEqualTo(key,value) 2.setParams(String params).目前只支持任选其一。

```java
 AMPost<String> post = new AMPost<>();
        post.setUrl("http://192.168.43.36:8090/blog/save")
            .addWhereEqualTo("title","最新报道")
            .addWhereEqualTo("content","tianjin")
            .setCacheControl(CacheControl.FORCE_NETWORK)
            .setTag(hashCode());

        post.addObjects(this, new OnAddListener<String>() {
            @Override
            public void onResponseSuccess(String response) {

            }

            @Override
            public void onResponseError(int code, @Nullable HttpError httpError) {

            }

            @Override
            public void onFailure(Exception e) {

            }
        });

```

### 3.AMUpload 上传

构造一个AMUpload对象。文件上传。如果传的Base64，则可以用AMPost对象。

```java
         AMUpload<Size> upload = new AMUpload<>();
        upload.setUrl("http://192.168.1.9:8090/blog/upload")
                .setFile(new File(getFilesDir().getAbsolutePath() + "/image.jpg"))
                .setFileName("image.jpg")
                .addWhereEqualTo("image","mark");

        upload.uploadObjects(this, new OnUploadListener<Size>() {
            @Override
            public void onRequestProgress(long progress, long total, boolean done) {
                Log.d("upload_success","progress= " + progress + ",total = " + total);
            }

            @Override
            public void onResponseSuccess(Size response) {
                Toast.makeText(MainActivity.this,response.getDesc(),Toast.LENGTH_LONG).show();
            }

            @Override
            public void onResponseError(int code, HttpError httpError) {

            }

            @Override
            public void onFailure(Exception e) {

            }
        });
```

### 4.AMDownload 下载
   构造一个AMDownload对象，设置FildCard.支持下载进度条。最后生成file对象。
```java
AMDownload<File> download = new AMDownload<>();
        download.setUrl("http://img0.utuku.china.com/550x0/news/20170528/1b3b24eb-44d4-4548-a40a-e6c089f6b4db.jpg")
                .setFileCard(new FileCard(
                        getCacheDir().getAbsolutePath(),
                        System.currentTimeMillis() + ".jpg"));

        download.downloadObjects(this, new OnDownloadListener<File>() {

            @Override
            public void onProgressChanged(long progress, long total) {
                Log.d(MainActivity.class.getSimpleName(),"progress = " + progress + ",total = " + total );
            }

            @Override
            public void onResponseSuccess(File response) {
                Glide.with(MainActivity.this).load(response.getAbsolutePath()).into(
                        imageView);
            }

            @Override
            public void onResponseError(int code, @Nullable HttpError httpError) {

            }

            @Override
            public void onFailure(Exception e) {

            }
        });
```


