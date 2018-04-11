---
title: DialogFragment实现带数字倒计时和小圆点进度的圆形进度条
date: 2016-07-06 
tags:
---

最近公司项目需要添加一个在网络不理想时，支付等待倒计时的弹框。开始想通过自定义view画出来，结果因为多个控件都有动画效果宣告失败。 最后利用继承Drawable再加上动画效果得以实现，效果如下：

![](http://img.blog.csdn.net/20160714110840520)

## 实现步骤

**没有交互的自定义view，可以实现一个drawable作为Imageview的背景**

**view的构成：**

- **外围圆环进度条**
- **圆环内倒计时数字**
- **圆环下方文字**
- **文字右边小圆点进度条**

###  自定义Drawable并初始化变量

```java
public class CountDownView extends Drawable {
    private static final String TAG = "CountDownView";
    private final static int PROGRESS_FACTOR = -360;
    private Paint mPaint;
    private Paint textPaint;
    private RectF mArcRect;
    private float radius;

    //当前进度条进度
    private float progress;
    //进度条颜色
    private int ringColor;
    //进度条宽度
    private int ringWidth;
    //倒计时数字
    private int showNumber;
    //字符串颜色
    private int textColor;
    //数字颜色
    private int numColor;

    private String showText = "正在支付";

    private int MAX_DOTS_COUNT = 3;

    Paint numPaint;


    //小圆点数量
    private int circularCount;

    private Paint dotPaint;

    private static final float PERCENT_CIRCLER_TO_HEIGHT = 3 / 14f;//半径占父控件高度的比例

    public CountDownView(int ringWidth, int ringColor, int showNumber, int textColor, int numColor) {
        mPaint = new Paint();
        numPaint = new Paint();
        mArcRect = new RectF();

        this.ringWidth = ringWidth;
        this.ringColor = ringColor;
        this.showNumber = showNumber;
        this.textColor = textColor;
        this.numColor = numColor;
    }

        @Override
        public void setAlpha(int alpha) {
            mPaint.setAlpha(alpha);
        }

        @Override
        public void setColorFilter(ColorFilter colorFilter) {

        }

        @Override
        public int getOpacity() {
            return mPaint.getAlpha();
        }
  }
```

### 实现draw方法

**画圆环**

```java
/**
  - 画圆环 *
  - @param bounds
  - @param canvas */
  private void drawRing(Rect bounds, Canvas canvas) {

     int size = bounds.height() > bounds.width() ? bounds.width() : bounds.height();

     radius = size * PERCENT_CIRCLER_TO_HEIGHT;

     mPaint.setColor(ringColor); mPaint.setStyle(Paint.Style.STROKE); mPaint.setStrokeCap(Paint.Cap.ROUND); mPaint.setAntiAlias(true); mPaint.setStrokeWidth(ringWidth);

     float cirX = bounds.centerX(); float cirY = bounds.centerY() * (27 / 35f);

     canvas.translate(cirX, cirY);

     mArcRect.set(-radius, -radius, radius, radius); canvas.drawArc(mArcRect, -90, progress, false, mPaint); }
```

**画倒计时数字**

```java
/**
    * 画倒计时数字
    *
    * @param canvas
    */
   private void drawNum(Canvas canvas) {
       float textSize = radius * 0.75f * 1.5f;
       mPaint.setTextSize(textSize);
       mPaint.setTextAlign(Paint.Align.CENTER);
       mPaint.setColor(textColor);
       mPaint.setStrokeWidth(ringWidth / 2);
       mPaint.setStyle(Paint.Style.FILL);
       float numX = 0;
       float numY = -(mPaint.descent() + mPaint.ascent()) / 2;
       canvas.drawText(Integer.toString(showNumber), numX, numY, mPaint);
   }
`
```

**画文字**

```java
/**
    * 画字符串
    *
    * @param canvas
    */
   private void drawText(Rect bounds, Canvas canvas) {
       textPaint = new Paint();
       textPaint.setAntiAlias(true);
       textPaint.setColor(textColor);
       textPaint.setTextAlign(Paint.Align.CENTER);
       textPaint.setStyle(Paint.Style.FILL);
       textPaint.setStrokeWidth(ringWidth / 2);
       float textSize = radius * 0.4f;
       textPaint.setTextSize(textSize);
       float textX = 0;
       float textY = radius * 1.8f - (textPaint.descent() + textPaint.ascent()) / 2;
       canvas.drawText(showText, textX, textY, textPaint);
   }
```

**画小圆点**

```java
/**
     * 画小圆点
     *
     * @param canvas
     */
    private void drawDots(Canvas canvas) {
        Rect textBound = new Rect();
        dotPaint = new Paint();
        dotPaint.setAntiAlias(true);
        dotPaint.setColor(textColor);
        dotPaint.setStyle(Paint.Style.FILL);
        dotPaint.setStrokeWidth(ringWidth / 2);

        textPaint.getTextBounds(showText, 0, showText.length(), textBound);
        float dotWidth = textBound.width() / showText.length();

        /**
         * 三个圆点占用宽度为一个字符所占宽度,设置每个圆点间隔为直径,第一个间距为一个半径,所以半径的计算方法为
         * 半径 = 一个字符宽度 / ((2*圆点个数-1)+1)
         */

        float cirRadius = dotWidth / ((2f * MAX_DOTS_COUNT - 1f) * 2f + 1f);
        float dotX = textBound.width() / 2;
        float dotY = radius * 1.8f - (textPaint.descent() + textPaint.ascent()) * 0.4f;
//        Log.d(TAG, "dotX= " + dotX + "\n" + "dotY=" + dotY);
        for (int i = 0; i < 4 * circularCount - 1; i += 4) {
            canvas.drawCircle(dotX + cirRadius * (i + 2), dotY, cirRadius, dotPaint);
        }
    }
```

**实现draw方法（调用以上画控件的方法即可）**

```java
@Override
   public void draw(Canvas canvas) {
       final Rect bounds = getBounds();
       drawRing(bounds, canvas);

       drawNum(canvas);
       drawText(bounds, canvas);
       drawDots(canvas);
   }
```

###  设置进度条以及倒计时数字和小圆点个数

```java
public int getCircularCount() {
        return circularCount;
    }

    public void setCircularCount(int circularCount) {
        this.circularCount = circularCount;
        invalidateSelf();
    }

    public int getShowNumber() {
        return showNumber;
    }

    public void setShowNumber(int showNumber) {
        this.showNumber = showNumber;
        invalidateSelf();
    }

    public float getProgress() {
        return progress / PROGRESS_FACTOR;
    }

    public void setProgress(float progress) {
        this.progress = progress * PROGRESS_FACTOR;
        invalidateSelf();

    }
```

### 在DialogFragment中使用drawable

**实现DialogFragment，并将countdownview设置为dialogfragment上一个imageview的背景图片**

```java
public class CountDownDialogFragment extends DialogFragment {

    private View rootView;
    ImageView countDown;

    private CountDownView mCdDrawable;
    private Animator mAnimator;
    CountDownDialogFragment dialog;
    private Window window;
    float width;

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        rootView = inflater.inflate(R.layout.count_down_dialog_frg, container, false);
        countDown = (ImageView) rootView.findViewById(R.id.count_down_iv);
        DisplayMetrics dm = getResources().getDisplayMetrics();
        width = dm.widthPixels;


        window = getDialog().getWindow();
        //背景透明
        window.setBackgroundDrawable(new ColorDrawable(Color.TRANSPARENT));
        //去掉标题
        getDialog().requestWindowFeature(Window.FEATURE_NO_TITLE);
        int whites = getResources().getColor(R.color.white);
        mCdDrawable = new CountDownView(10, whites, 10, whites, whites);
        countDown.setImageDrawable(mCdDrawable);

        if (mAnimator != null) {
            mAnimator.cancel();
        }
        countDown.setVisibility(View.VISIBLE);
        mAnimator = prepareAnimator();
        mAnimator.start();
        return rootView;
    }
```

**使用属性动画，计算进度条progress以及倒计时数字和小圆点的变化规律**

```java
private Animator prepareAnimator() {
        AnimatorSet animation = new AnimatorSet();

        //进度条动画
        ObjectAnimator progressAnimator = ObjectAnimator.ofFloat(mCdDrawable, "progress", 1f, 0f);
        progressAnimator.setDuration(10000);
        progressAnimator.setInterpolator(new LinearInterpolator());
        progressAnimator.addListener(new Animator.AnimatorListener() {
            @Override
            public void onAnimationStart(Animator animation) {

            }

            @Override
            public void onAnimationEnd(Animator animation) {
                countDown.setVisibility(View.GONE);
                if (dialog != null)
                    dialog.dismiss();
            }

            @Override
            public void onAnimationCancel(Animator animation) {
                countDown.setVisibility(View.GONE);
                if (dialog != null)
                    dialog.dismiss();
            }

            @Override
            public void onAnimationRepeat(Animator animation) {

            }
        });

        // 居中的倒计时数字
        ObjectAnimator showNumAnimator = ObjectAnimator.ofInt(mCdDrawable, "showNumber", 10, 0);
        showNumAnimator.setDuration(10000);
        showNumAnimator.setInterpolator(new LinearInterpolator());

        //小圆点进度条
        ObjectAnimator dotProgressAnimator = ObjectAnimator.ofInt(mCdDrawable, "circularCount", 0, 4);
        dotProgressAnimator.setDuration(1500);
        dotProgressAnimator.setRepeatCount(ValueAnimator.INFINITE);
        dotProgressAnimator.setRepeatMode(ValueAnimator.RESTART);
        dotProgressAnimator.setInterpolator(new LinearInterpolator());

        animation.playTogether(progressAnimator, showNumAnimator, dotProgressAnimator);
        return animation;

    }
```

**修改DialogFragment窗体大小，需要在onResume方法中实现**

```java
@Override
   public void onResume() {
       super.onResume();
       dialog = this;
       //设置大小
       window.setLayout((int) (width * 0.4f), (int) (width * 0.4f * (7 / 8f)));
   }
```

### 源码请到[Github](https://github.com/lvandroid/CountDownView)
