---
title: 有进度条的Webview
date: 2016-07-07 
tags:
---

# 带进度条的WebVeiw实现以及进度条和webview之间有空隙的解决方法

给WebView顶部添加加载进度条是非常常用的功能，实现方法也很简单，主要有两种方案：

## 解决思路

- 在布局文件里直接添加ProgressBar

简单粗暴，但是自带的ProgressBar的样式固定，不可控，很可能不满足项目需求

- 集成在WebView里

定制灵活，可完美解决进度条与webview之间的空隙的BUG

## 实现方法

### 直接在布局文件里实现

布局文件如下：webview_layout.xml

```xml

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">
    <ProgressBar
        style="?android:attr/progressBarStyleHorizontal"
        android:id="@+id/progressbar"
        android:layout_width="match_parent"
        android:layout_height="5dp"/>

    <WebView
        android:id="@+id/webview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</LinearLayout>
```

代码如下：

```java
public class WebViewActivity extends BaseActivity  {
  @Override
   public void onCreate(Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       setContentView(R.layout.webview_layout);
       webView = (WebView) findViewById(R.id.webview);
       progressbar =(ProgressBar) findViewById(R.id.progressbar);

      //重写WebChromeClient的onProgressChanged()方法显示进度
       webView.setWebChromeClient(new WebChromeClient() {
         @Override
         public void onProgressChanged(WebView view, int newProgress) {
             if (newProgress == 100) {
                 progressbar.setVisibility(View.INVISIBLE);
             } else {
                 if (View.INVISIBLE == progressbar.getVisibility()) {
                     progressbar.setVisibility(View.VISIBLE);
                 }
                 progressbar.setProgress(newProgress);
             }
             super.onProgressChanged(view, newProgress);
         }

     });
       }
}
```

### 集成在WebView控件中

该方法需要将progressbar集成到webview，需要重写webview并自定义progressbar的样式

- ##### 自定义progressbar样式：

progress_bar_states.xml

```xml
<?xml version="1.0" encoding="utf-8"?>  
<!--WebView顶端的进度条的样式-->  
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">  
    <!-- 进度条背景色 -->
    <item android:id="@android:id/background">  
        <shape>  
            <corners android:radius="2dp" />  
            <gradient  
                android:angle="270"  
                android:centerColor="#E3E3E3"  
                android:endColor="#E6E6E6"  
                android:startColor="#C8C8C8" />  
        </shape>  
    </item>  
    <!--加载中的进度条的样式-->
    <item android:id="@android:id/progress">  
        <clip>  
            <shape>  
                <corners android:radius="2dp" />  
                <!-- 颜色可随便定义为自己需要的颜色 -->
                <gradient  
                    android:centerColor="#4AEA2F"  
                    android:endColor="#31CE15"  
                    android:startColor="#5FEC46" />  
            </shape>  
        </clip>  
    </item>  
</layer-list>
```

- ##### 重写Webview

WebViewWithProgress.java

```java
public class WebViewWithProgress extends WebView {
    private ProgressBar progressBar;

    public WebViewWithProgress(Context context, AttributeSet attrs) {
        super(context, attrs);
        /** 在initView之前先判断了一个情况： isInEditMode()， 加入这个函数是不会在XML文件
          * 打开时弹出一个冗长的错误。也可以不判断
          */
        if (!isInEditMode()) {
            initView(context);
        }
        getSettings().setDomStorageEnabled(true);

    }

    private void initView(Context context) {
        progressBar = new ProgressBar(context, null, android.R.attr.progressBarStyleHorizontal);
        LayoutParams params = new LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, 10, 0, 0);
        progressBar.setLayoutParams(params);
        Drawable drawable;
        if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.LOLLIPOP) {
            drawable = context.getResources().getDrawable(R.drawable.progress_bar_states, null);
        } else {
            drawable = context.getResources().getDrawable(R.drawable.progress_bar_states);

        }
        progressBar.setProgressDrawable(drawable);
        addView(progressBar);

    }

    public ProgressBar getProgressBar() {
        return progressBar;
    }

  }
```

- ##### 布局文件

布局文件直接使用用WebViewWithProgress

```xml
...

<com.bsty.library.webview.WebViewWithProgress
   android:id="@+id/webview"
   android:layout_width="match_parent"
   android:layout_height="match_parent"
   />

...
```

- ##### 代码调用自定义的Webview

调用的代码也直接调用，就如初始的Webview一样

```java
ProgressBar progressBar;
private WebViewWithProgress webView;
@Override
   public void onCreate(Bundle savedInstanceState) {
         super.onCreate(savedInstanceState);
         setContentView(R.layout.webview_layout);
         webView = (WebViewWithProgress) findViewById(R.id.webview);
         progressBar = webview.getProgressBar();


         progressBar = webView.getProgressBar();
          //重写WebChromeClient的onProgressChanged()方法显示进度
         webView.setWebChromeClient(new WebChromeClient() {
           @Override
           public void onProgressChanged(WebView view, int newProgress) {
               if (newProgress == 100) {
                   progressbar.setVisibility(View.INVISIBLE);
               } else {
                   if (View.INVISIBLE == progressbar.getVisibility()) {
                       progressbar.setVisibility(View.VISIBLE);
                   }
                   progressbar.setProgress(newProgress);
               }
               super.onProgressChanged(view, newProgress);
           }

       });
```
