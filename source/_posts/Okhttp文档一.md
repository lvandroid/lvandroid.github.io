---
title: Okhttp文档一
date: 2016-07-01 
tags:
---

## 配置

权限设置

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

添加依赖到app/build.gradle文件:

```gradle
compile 'com.squareup.okhttp3:okhttp:3.3.0'
```

## 发送和接收网络请求

首先，必须实例化一个OKHttpClient并且创建一个Request对象：

```java
//should be a singleton
OkHttpClient client = new OKHttpClient();

Request request = new Request.Builder()
                        .url("http://publicobject.com/helloworld.txt")
                        .build();
```

如果需要请求参数，那么OKHttp中HttpUrl类提供构造方法构造URL：

```java
    HttpUrl.Builder urlBuilder = HttpUrl.parse("https://ajax.googleapis.com/ajax/services/search/images").newBuilder();
    urlBuilder.addQueryParameter("v", "1.0");
    urlBuilder.addQueryParameter("q", "android");
    urlBuilder.addQueryParameter("rsz", "8");
    String url = urlBuilder.build().toString();

    Request request = new Request.Builder()
                            .url(url)
                            .build();
```

认证参数和headers也可以添加到请求：

```java
Request request = new Request.Builder()
        .header("Authorization","token abcd")
        .url("https://api.github.com/users/codepath")
        .build();
```

## 同步网络调用

我们可以创建一个Call对象并且同步调度网络请求：

```java
Response response = client.newCall(request).execute();
```

因为android不允许网络在主线程中请求网络，只能在子线程或者后台进程中同步请求，在轻量级的网络请求中也可以使用AsyncTask.

## 异步网络请求

我们也可以创建一个Call对象发起异步网络请求，使用enqueue()方法，并且通过匿名回调对象，实现onFailure()方法和onResponse()方法。

```java
//Get a handler that can be used to post to the main thread
client.newCall(request).enqueue(new Callback(){
    @override
    public void onFailure(Call call, IOException e){
        e.printStackTrace();
    }

    @Override
    public void onResponse(Call call, final Response response) throws IOException{
        if(!response.isSuccessful()){
            throw new IOException("Unexpected code  "+ response);
        }
    }
});
```

如果需要更新UI，可以使用 runOnUiThread()或者post结果到主线程.

```java
client.newCall(request).enqueue(new Callback(){
    @override
    public void onResponse(Call call, final Response response) throws IOExcepiton{
        // ... check for failure using `isSuccessful` before proceeding

        // Read data on the worker thread
        final String responseData = response.body().string();

        // Run view-related code back on the main thread
        MainActivity.this.runOnUiThread(new Runnable(){
            @Override
            public void run(){
                try{
                    TextView myTextView = (TextView) findViewById(R.id.myTextView);
                    myTextView.setText(responseData);
                }catch(IOException e){
                    e.printStackTrace();
                }
     }
        })
    }
});
```

## 处理网络响应

如果请求没有被取消并且没有连接问题， onResponse()方法将会执行，通过response对象可以检测状态码，返回响应体，头。如果返回码是2开头的则调用isSuccessful()方法，如 200,201等。

```java
if(!response.isSuccessful()){
    throw new IOException("Unexpected code" + response);
}
```

响应头将提供一个列表

```java
Headers responseHeaders = response.headers();
for(int i = 0; i<responseHeaders.size();i++){
    Log.d("DEBUG",responseHeaders.name[i]+": " + responseHeaders.value(i));
}
```

头也可以直接通过response.header()获取

```java
String  header = response.header("Date");
```

也可以通过调用response.body()获取响应数据，然后调用string()方法。response.body()只能执行一次并且需要在后台进程执行。

```java
Log.d("DEBUG", response.body().string());
```

## 处理JSON数据

假设我们调用GitHub API，返回基于JSON的数据：

```java
Request request = new Request.Builder()
            .url("https://api.github.com/users/codepath")
            .build();
```

也可以通过转换成JSONObject或者JSONArray解析响应数据：

```java
client.newCall(request).enqueue(new Callback(){
    @Override
    public void onResponse(Call call, final Response response) throws IOException{
        try{
            String responseData = response.body().string();
            JSONObject json = new JSONObject(responseData);
            final String owner = json.getString("name");
        } catch(JSONException e){

        }
    }
})
```

## 使用GSON处理JSON

响应体的string()方法将为加载整个数据到内存中。为了使内存使用更高性能，使用charStream()方法将响应转换成流。

使用Gson库，必须根据响应的JSON先定义一个类

```java
static class GitUser{
    String name;
    String url;
    int id;
}
```

然后使用Gson解析器将数据直接转换成java类：

```java
// Create new gson object
final Gson gson = new Gson();
//Get a handler that can be used to post to the main thread
client.newCall(request).enqueue(new Callback(){
    //Parse response using gson deserializer
    @Override
    public void onResponse(Call call, final Response response) throws IOExcepiton{
    //Process the data on the worker thread
        GitUser user = gson.fromJson(rsesponse.body().charStream(), GitUser.class)
        //Access deserialized user object here
    }
})
```

## 发送认证请求

OkHttp有一个用来修改使用拦截出站请求的机制,一个常见的用例是OAuth协议，这需要请求使用私钥签名,该OkHttp路标库与库路标工程使用拦截签署每个请求。通过这种方式，调用者不需要记住签署每个请求：

```java
OkHttpOAuthConsumer consumer = new OkHttpOAuthConsumer(CONSUMER_KEY, CONSUMER_SECRET);
consumer.setTokenWithSecret(token, secret);
okHttpClient.interceptors().add(new SigningInterceptor(consumer));
```

## 缓存网络响应

可以通过创建OkHttpClient时设置网络缓存：

```java
int cacheSize = 10*1024*1024; //10MiB
Cache cache = new Cache(getApplication().getCacheDir(), cacheSize);
OkHttpClient client = new OkHttpClient.Builder().cache(cache).build();
```

可以通过设置cacheControl属性控制是否取回响应的缓存，例如：假如只想在数据缓存中取回请求，可以如下构建Request:

```java
Request request = new Request.Builder()
                    .url()
                    .cacheControl(new CacheControl.Builder().onlyIfCached().build())
                    .build();
```

也可以通过使用noCache()强制网络响应:

```java
.cacheControl(new CacheControl.Builder().noCache().build())
```

也可以指定最大缓存时间：

```java
.cacheControl(new CacheContorl.Builder().maxStale(365,TimeUnit.DAYS).build())
```

为了取回缓存的响应，可以简单的调用cacheResponse()：

```java
Call call = client.newCall(request);
call.enqueue(new Callback(){
    @Override
    public void onFailure(Call call, IOException e){

    }
    @Override
    public void onResponse(Call call, final Response response) throws IOException{
        final Response text = response.cacheResponse();
        if(text!=null){
            Log.d("here", text.toString());
        }
    }
})
```

## 故障排除

OkHttp可以努力试着在抽象的库中各个层时一步步排除故障。

## HttpLog拦截器

添加HttpLogInterceptor到依赖

```gradle
compile 'com.squareup.okhttp3:loging-interceptor:3.3.0'
```

你需要给HttpLogInterceptor添加网络拦截器:

```java
OkHttpClient.Builder builder = new OkHttpClient.Builder();

HttpLoggingInterceptor httpLoggingInterceptor = new HttpLoggingInterceptor();
/ Can be Level.BASIC, Level.HEADERS, or Level.BODY
// See http://square.github.io/okhttp/3.x/logging-interceptor/ to see the     options.

httpLoggingInterceptor.setLevel(HttpLoggingInterceptor.Level.BODY);
builder.networkInterceptors().add(httpLoggingInterceptor);
builder.build();
```

## Stetho

用chrome访问网络时需要使用Facebook的Stetho插件 添加gradle配置

```gradle
dependencies{
    compile 'com.facebook.stetho:stetho-okhttp3:1.3.0'
}
```

当实例化OkHttp时，确保通过StethoInterceptor

```java
OkHttpClient client = new OkHttpClient.Builder()
    .addNetworkInterceptor(new SethoInterceptor())
    .build();
```

最后，确保在Application里初始化了Stetho

```java
public class MyApplication extends Application{
    public void onCreate(){
        super.onCreate();
        Stetho.initializeWithDefaults(this);
    }
}
```
