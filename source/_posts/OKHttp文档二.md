---
title: OKHttp文档二
date: 2016-07-01 
tags:
---

## 同步获取

下载一个文件，以字符串形式打印他的头和响应体.

string()方法对于小的文档来说方便且高效，但是如果响应体大于1M, 避免使用string()，因为会加载到内存中，这种情况下，将响应体以流的方式处理更好。

```java
  private final OkHttpClient client = new OkHttpClient();

  public void run() throw Exception{
    Request request = new Request.Builder()
        .url("http://publicobject.com/helloworld.txt")
        .build();

    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()) throw new IOException("Unexpected code "+response);       

    Headers responseHeaders = response.headers();
    for (int i =0;i<responseHeaders.size() ;i++ ) {
      System.out.println(responseHeaders.name(i)+": "+responseHeaders.value(i));
    }
    System.out.println(response.body().string());
  }
```

## 异步获取

在一个工作线程上下载一个文件,当响应可读时得到回调，在响应头准备好后回调，当阻塞时读取响应体，OkHttp目前不提供异步接收部分响应体的API

```java
  private final OkHttpClient client = new OkHttpClient();

  public void run() throw Exception{
    Request request = new Request.Builder()
      .url("http://publicobject.com/helloworld.txt")
      .build();

    client.newCall(request).enqueue(new Callback(){
      @Override
      public void onFailure(Call call, IOException e){
        e.printStackTrace();
      }

      @Override
      public void onResponse(Call call, Response response)throw IOException{
        if (!response.isSuccessful())throw new IOException("Unexpected code "+ response);

        Headers responseHeaders = response.headers();
        for (int i =0;size=responseHeaders.size() ; i++) {
          System.out.println(responseHeaders.name(i)+": "+responseHeaders.value(i));
        }

        System.out.println(response.body().string());
      }
    })
  }
```

## 访问头

通常情况下HTTP头格式相似 Map

<string,string>： 每一个键对应一个值或者没有值，但是一些头允许多个值，比如HTTP响应支持多个变化的头是合法和常见的，OKHttp的api试图使两种情况更方便。</string,string>

当写请求头时，使用header(name,value)键值对，使得值只有唯一的名称. 新添加值之前，已经存在的值将会被删除，使用方法addHeader(name, value)方法在没有移除已经存在的值的情况下添加一个头。

当读取响应头时，使用方法header(name)返回最后一个命名的值。如果没有展示任何值，方法header(name)将返回null，headers(name)方法将以列表形式读取所有的值.

Headers类可以通过index获取，可以用来访问所有的headers。

```java
  private final OkHttpClient client = new OkHttpClient();

  public void run() throws Exception{
    Request request = new Request.Builder()
      .url("https://api.github.com/repos/square/okhttp/issues")
      .header("User-Agent","OkHttp Headers.java")
      .addHeader("Accept","application/json; q=0.5")
      .addHeader("Accept", "application/vnd.github.v3+json")
      .build();

    Response rsponse = client.newCall(request).execute();
    if (!response.isSuccessful())throw new IOException("Unexpected code "+ response);

    System.out.println("Server: " + response.header("Server"));
    System.out.println("Date: "+ response.header("Date"));
    System.out.println("Vary: "+ response.headers("Vary"));

  }
```

## Post一个字符串

使用HTTP POST请求发送一个请求到服务器， 这个例子发送一个markdown文件到web服务器并将markdown文件呈现为html文件。因为整个请求体是同事在内存中的，使用该api避免post超过1M的大文件.

```java
  public static final MediaType MEDIA_TYPE_MARKDOWN
      =MediaTyep.parse("text/x-markdwon; charset=utf-8");

  private final OKhttpClient client = new OKhttpClient();

  public void run()throws Exception{
    String postBody =""
      + "Releases\n"
      + "--------\n"
      + "\n"
      + " * _1.0_ May 6, 2013\n"
      + " * _1.1_ June 15, 2013\n"
      + " * _1.2_ August 11, 2013\n";

  Request request = new Request.Builder()
      .url("https:/api.github.com/markdown/raw")
      .post(RequestBody.create(MEDIA_TYPE_MARKDOWN, postBody))
      .build();
  }
```

## Post 流

怎么以流的形式去请求， 请求体的内容当被写入的时候生成，项目可能提供一个OutputStream，可以调用BufferedSink.outputStream()方法获取到。

```java
  public static final MediaType MDEIA_TYPE_MARKDOWN
    =MediaType.parse("text/x-markdown; charset=utf-8");

  private final OkHttpClient client =new OkHttpClient();

  public void run() throws Exception{
    RequestBody requestBody = new RequestBody(){
      @Override
      public MediaType contentType(){
        return MEDIA_TYPE_MARKDOWN;
      }

    @Override
    public void writeTo(BufferedSink sink)throws IOException{
      sink.writeUtf8("Numbers\n");
      sink.writeUtf8("------\n");
      for (int i =2 ;i<=997 ;i++ ) {
        sink.writeUtf8(String.fromat(" * %s = %s\n", i, factor(i)));
      }
    }

    private String factor(int n){
      for(int i = 2; i<n; i++){
        int x = n/i;
        if(x* i==n)return factor(x)+ " x "+ i;
      }
      return Integer.toString(n);
    }
  };

  Request request = new Request.Builder()
    .url("https://api.github.com/markdown/raw")
    .post(requestBody)
    .build();

  Response response = client.newCall(request).execute();
  if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
  System.out.println(response.body().string());
  }
```

## Post 文件

使用一个文件作为请求体比较容易

```java
  public static final MediaType MEDIA_TYPE_MARKDOWN
    = MediaType.parse("text/x-markdown; charset = utf-8");

  private final OkHttpClient client = new OkHttpClient();

  public void run() throws Exception{
      File file = new File("README.md");
      Request request= new Request.Builder()
        .url("https://api.github.com/markdown/raw")
        .post(RequestBody.create(MEDIA_TYPE_MARKDOWN, file))
        .build();
      Response response = client.newCall(request).execute();
      if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

      System.out.println(response.body().string());

  }
```

## 发布表单参数

使用 `FromBody.Builder`构建一个请求体，就像一个HTML的`<form>`标签一样， 键值会以兼容HTML的表单URL编码进行编码

```java
  private final OkHttpClient client = new OkHttpClient();

  public void run() throws Exception{
    RequestBody fromBody = new FormBody.Builder()
      .add("search", "Jurassic Park")
      .build();

    Request request = new Request.Builder()
      .url("https://en.wikipedia.org/w/index.php")
      .post(formBdoy)
      .build();

    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    System.out.println(response.body().string());  
  }
```

## 混合请求

`MultipartBody.Builder`可以建立HTML文件上传兼容的复杂表单请求体，复杂请求体的每一部分本身就是请求主体，并且可以定义自己的头,比如他的 `Content-Disposition`,如果可用，`Content-Length`和`Content-Type`可以自动添加。

```java
  private static final String IMGUR_CLIENT_ID = "...";
  private static final MediaType MEDIA_TYPE_PNG = MediaType.parse("image/png");
  private final OkHttpClient client = new OkHttpClient();
  public void run() throws Exception {
    // Use the imgur image upload API as documented at https://api.imgur.com/endpoints/image
    RequestBody requestBody = new MultipartBody.Builder()
        .setType(MultipartBody.FORM)
        .addFormDataPart("title", "Square Logo")
        .addFormDataPart("image", "logo-square.png",
            RequestBody.create(MEDIA_TYPE_PNG, new File("website/static/logo-square.png")))
        .build();

    Request request = new Request.Builder()
        .header("Authorization", "Client-ID " + IMGUR_CLIENT_ID)
        .url("https://api.imgur.com/3/image")
        .post(requestBody)
        .build();

    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    System.out.println(response.body().string());
  }
```

## 使用Gson解析响应的JSON

GSON是JSON和Java对象之间的转换很方便的API。这里，我们用它来解码从GitHub的API JSON响应。 需要注意的是`ResponseBody.charStream（）`使用的`Content-Type`响应头选择响应体解码时所使用的字符集。如果没有指定字符集默认为UTF-8。

```java

private final OkHttpClient client = new OkHttpClient();
private final Gson gson = new Gson();

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("https://api.github.com/gists/c2a7c39532239ff261be")
      .build();
  Response response = client.newCall(request).execute();
  if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

  Gist gist = gson.fromJson(response.body().charStream(), Gist.class);
  for (Map.Entry<String, GistFile> entry : gist.files.entrySet()) {
    System.out.println(entry.getKey());
    System.out.println(entry.getValue().content);
  }
}

static class Gist {
  Map<String, GistFile> files;
}

static class GistFile {
  String content;
}
```

## 响应缓存

要缓存响应，需要可以读取和写入的缓存目录，高速缓存的大小有限制。缓存目录应该是私有的，不信任的应用程序不应该能够阅读其内容！

它是有多个缓存同时访问相同的高速缓存目录中的错误。大多数应用程序应该用自己的高速缓存配置调用新OkHttpClient（）一次，，都使用相同的实例。否则，实例将被二级缓存覆盖，破坏响应缓存，程序可能崩溃。

响应缓存使用HTTP标头的所有配置。您可以添加和请求头一样的Cache-Control：max-stale= 3600 和 OkHttp的缓存会为他们服务。网络服务器配置的响应多长时间缓存有自己的响应头，如缓存控制：max-age= 9600。有缓存头强制缓存的响应，强制网络响应，或强制使用条件GET验证的网络响应。

```java
 private final OkHttpClient client;

public CacheResponse(File cacheDirectory) throws Exception {
 int cacheSize = 10 * 1024 * 1024; // 10 MiB
 Cache cache = new Cache(cacheDirectory, cacheSize);

 client = new OkHttpClient.Builder()
     .cache(cache)
     .build();
}

public void run() throws Exception {
 Request request = new Request.Builder()
     .url("http://publicobject.com/helloworld.txt")
     .build();

 Response response1 = client.newCall(request).execute();
 if (!response1.isSuccessful()) throw new IOException("Unexpected code " + response1);

 String response1Body = response1.body().string();
 System.out.println("Response 1 response:          " + response1);
 System.out.println("Response 1 cache response:    " + response1.cacheResponse());
 System.out.println("Response 1 network response:  " + response1.networkResponse());

 Response response2 = client.newCall(request).execute();
 if (!response2.isSuccessful()) throw new IOException("Unexpected code " + response2);

 String response2Body = response2.body().string();
 System.out.println("Response 2 response:          " + response2);
 System.out.println("Response 2 cache response:    " + response2.cacheResponse());
 System.out.println("Response 2 network response:  " + response2.networkResponse());

 System.out.println("Response 2 equals Response 1? " + response1Body.equals(response2Body));
}
```

为了防止使用缓存的响应，使用CacheControl.FORCE_NETWORK。为了防止使用网络，使用CacheControl.FORCE_CACHE。警告：如果您使用FORCE_CACHE并且响应要求网络，OkHttp会返回504 （不满足请求的响应）

## 取消请求

使用`Call.cancel()` 立即停止正在发起的请求。 如果线程正在写请求或者读响应，会得到一个IOException。 这样使用当不再需要请求的时候可以保护网络，比如当你的用户在使用导航的时候。 同步和异步都可以被取消。

```java
 private final ScheduledExecutorService executor = Executors.newScheduledThreadPool(1);
 private final OkHttpClient client = new OkHttpClient();

 public void run() throws Exception {
   Request request = new Request.Builder()
       .url("http://httpbin.org/delay/2") // This URL is served with a 2 second delay.
       .build();

   final long startNanos = System.nanoTime();
   final Call call = client.newCall(request);

   // Schedule a job to cancel the call in 1 second.
   executor.schedule(new Runnable() {
     @Override public void run() {
       System.out.printf("%.2f Canceling call.%n", (System.nanoTime() - startNanos) / 1e9f);
       call.cancel();
       System.out.printf("%.2f Canceled call.%n", (System.nanoTime() - startNanos) / 1e9f);
     }
   }, 1, TimeUnit.SECONDS);

   try {
     System.out.printf("%.2f Executing call.%n", (System.nanoTime() - startNanos) / 1e9f);
     Response response = call.execute();
     System.out.printf("%.2f Call was expected to fail, but completed: %s%n",
         (System.nanoTime() - startNanos) / 1e9f, response);
   } catch (IOException e) {
     System.out.printf("%.2f Call failed as expected: %s%n",
         (System.nanoTime() - startNanos) / 1e9f, e);
   }
 }
```

## 超时

使用超时时，其对不可达或者失败的调用。网络分区：可以是由于客户端连接问题，服务器可用性的问题，或其他的任何东西。 OkHttp支持连接，读取和写入超时。

```java
private final OkHttpClient client;

public ConfigureTimeouts() throws Exception {
 client = new OkHttpClient.Builder()
     .connectTimeout(10, TimeUnit.SECONDS)
     .writeTimeout(10, TimeUnit.SECONDS)
     .readTimeout(30, TimeUnit.SECONDS)
     .build();
}

public void run() throws Exception {
 Request request = new Request.Builder()
     .url("http://httpbin.org/delay/2") // This URL is served with a 2 second delay.
     .build();

 Response response = client.newCall(request).execute();
 System.out.println("Response completed: " + response);
}
```

## 单个请求配置

所有的HTTP客户端都在OkHttpClient中配置，包括代理设置，超时和缓存。当你需要改变单一调用的配置，调用OkHttpClient.newBuilder（）。这将返回共享相同的连接池，调度和配置与原来的客户端生成器。在下面的例子中，我们做了500毫秒超时，另外一3000毫秒超时一个请求。

```java
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("http://httpbin.org/delay/1") // This URL is served with a 1 second delay.
      .build();

  try {
    // Copy to customize OkHttp for this request.
    OkHttpClient copy = client.newBuilder()
        .readTimeout(500, TimeUnit.MILLISECONDS)
        .build();

    Response response = copy.newCall(request).execute();
    System.out.println("Response 1 succeeded: " + response);
  } catch (IOException e) {
    System.out.println("Response 1 failed: " + e);
  }

  try {
    // Copy to customize OkHttp for this request.
    OkHttpClient copy = client.newBuilder()
        .readTimeout(3000, TimeUnit.MILLISECONDS)
        .build();

    Response response = copy.newCall(request).execute();
    System.out.println("Response 2 succeeded: " + response);
  } catch (IOException e) {
    System.out.println("Response 2 failed: " + e);
  }
}
```

## 认证处理

OkHttp可以自动重试未经授权的请求。当响应为401：没有被授权，Authenticator被要求提供凭据。实现应该建立一个包括缺少凭据的新要求。如果没有凭证可用，则返回null跳过重试。 使用Response.challenges（）来获取任何身份验证挑战计划和领域。当完成一个基本的挑战，用Credentials.basic（用户名，密码），以请求头编码。

```java
private final OkHttpClient client;

public Authenticate() {
  client = new OkHttpClient.Builder()
      .authenticator(new Authenticator() {
        @Override public Request authenticate(Route route, Response response) throws IOException {
          System.out.println("Authenticating for response: " + response);
          System.out.println("Challenges: " + response.challenges());
          String credential = Credentials.basic("jesse", "password1");
          return response.request().newBuilder()
              .header("Authorization", credential)
              .build();
        }
      })
      .build();
}

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("http://publicobject.com/secrets/hellosecret.txt")
      .build();

  Response response = client.newCall(request).execute();
  if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

  System.out.println(response.body().string());
}
```

为了避免验证时不重试，你可以返回null放弃。例如，当这些确切的凭据已经尝试您可以跳过重试：

```java
if (credential.equals(response.request().header("Authorization"))) {
 return null; // If we already failed with these credentials, don't retry.
}
```

当打一个应用程序尝试被限制,您也可以跳过重试：

```java
if (responseCount(response) >= 3) {
   return null; // If we've failed 3 times, give up.
 }
```

上面的代码依赖于responseCount（）方法：

```java
private int responseCount(Response response) {
    int result = 1;
    while ((response = response.priorResponse()) != null) {
      result++;
    }
    return result;
  }
```

