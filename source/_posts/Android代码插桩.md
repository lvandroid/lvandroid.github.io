---
title: Android代码插桩
date: 2017-08-06 
tags:
---

## 定个小目标

为了采集android网络性能数据，最简单直接的方式是我们可以在每个网络请求方法中打印相关数据日志上传。然而在生产环境中明显不可行，我们需要实现非侵入的，全量的数据采集，那么AOP成了关键。下面以网络性能数据收集SDK开发为例说明怎样一步步实现Android上的AOP。

## 聊聊AOP

### 生活中AOP的影子

"什么时候出发？"  

"明天"

"累了就回家"

这句“累了就回家”就是生活中的AOP，临行前的叮嘱，期间的问候，回家后的温暖，这是AOP做的事,和出门要做的事并没有什么关系。

### Android AOP方式

AOP实现原理上可以分为运行时AOP和编译时AOP：

**运行时AOP：**hook某些关键方法,主流框架有Dexposed,Xposed等

**编译时AOP：**apk打包过程中对class文件的字节码进行扫描更改,主流框架aspactJ,ASM。

***不同工具库优劣势对比***

|工具库|方式|能力|缺点|学习曲线|
|---|---|---|---|---|
|XPosed|运行时hook|1. 能hook自己应用进程的方法；</br> 2. 能hook别的应用的方法；</br> 3. 能hook系统方法；|1. 手机需要root;</br>2. 依赖三方包的支持，碎片化严重兼容性差；|一般|
| DexPosed	| 运行时hook	|能hook自己应用进程的方法；|1. 目前不支持4.4以及5.1以上的系统；</br> 2. 依赖三方包的支持，碎片化严重兼容性差；|一般|
| AspectJ |编译时字节码注入|可以在编译成字节码的过程中插入代码；|官方有Eclipse插件，但没有Android Studio插件，需要替换编译器，环境不好部署；|一般|
|ASM|编译时或者运行期字节码注入|可以在字节码中文件或者ClassLoader加载字节码的时候插入代码；|需要熟悉字节码语法；|陡峭|


***我们采用的AOP方式：***

SDK的难点是数据的采集，手动埋点的方式无疑是行不通的，一方面代价太大且容易产生错误，另一方面对于没有源代码的第三方库我们无法直接修改，因而不能满足我们的需求。我们选择在应用构建期间通过修改字节码的方式来进行代码插桩。

应用构建过程：

![打包流程](https://ws2.sinaimg.cn/large/006tKfTcgy1fj8huamog8j30ew0oiac0.jpg)

应用中所有的class文件包括引用的第三方库中的class，都会经由dex过程，被转化为一个或者多个dex文件，正因为所有的class文件都会在dex这一步被处理，所以我们选择在这里进行字节码插桩。hook Android编译打包流程并借助ASM库对项目字节码文件进行统一扫描，过滤以及修改。

## 说点细节

###  一个简单的demo
```java
URL mURL = new URL(url);
HttpURLConnection conn = (HttpURLConnection) mURL.openConnection();
```

如果我们想对openConnection()这个方法动点手脚，比如在执行方法开始时打印系统时间，但不能影响其他逻辑。那么我们可以新建一个代理类Monitor:

```java
public class Monitor{
	private static final String TAG="Monitor";
	public static HttpURLConnection hookOpenConnection(URL url){
		Log.d(TAG,System.currentTimeMillis());
		//原方法逻辑不能修改
		return (HttpURLConnection)url.openConnection();
	}
}
```

如果编译修改后的方法变成这样

```java
URL mURL = new URL(url);
HttpURLConnection conn = Monitor.hookOpenConnection(url);
```
那么目的就达到了，如何去替换成我们的代理类呢，不得不说我们的主角ASM和gradle了。


>***我们要做的：***
>
>1. gradle插件
>
>2. ASM修改字节码
>
>3. 业务代码


###  插桩入口 transform api

Android Gradle Plugin 1.5.0后提供了transform api用作字节码插桩的入口。细节可查看[官方API](http://tools.android.com/tech-docs/new-build-system/transform-api)。

对于1.5.0版本一下的情况，可hook dex.jar实现，具体实现可参考博客[APM之原理篇](http://blog.csdn.net/sgwhp/article/details/50239747)。

### 字节码操作工具ASM

ASM是一个java字节码操纵框架，它能被用来动态生成类或者增强既有类的功能。ASM 可以直接产生二进制 class 文件，也可以在类被加载入 Java 虚拟机之前动态改变类行为。类似功能的工具库还有javassist，BCEL等。

使用不同工具库生成同一个类的耗时：

|Framework  | First time  | Later times|  
|---|:---:|:---:|
|Javassist  | 257  | 5.2  |
|BCEL  | 473  |  5.5|
|ASM  | 62.4  |  1.1|

因为灵活强大高效，所以选择ASM作为字节码操作工具。

#### ASM API简介

ASM（core api） 按照visitor模式按照class文件结构依次访问class文件的每一部分，有如下几个重要的visitor。

###### ClassVisitor

```java
public abstract class ClassVisitor{
    public ClassVisitor(int api);
    public ClassVisitor(int api, ClassVisitor cv);
    //访问类名，父类名，实现的接口（数组）等信息
    public void visit(int version, int access, String name,String signature, String superName, String[] interface);
    //访问内部类
    public void visitInnerClass(String name, String outerName, String innerName, int access);
    //访问字段
    public FieldVisitor visitField(int access , String name, String desc, String signature, Object value);
    //访问方法
    public MethodVisitor visitMethod(int access, String name ,String desc, String signature, String[] Exceptions);
    void visitEnd();
}
```


###### MethodVisitor

```java
public abstract class MethodVisitor{
    //开始访问方法体内的代码
    public void visitCode();
    //访问方法的try catch block
    public void visitTryCatchBlock(Label start, Label end, Label handler, String type);
    //指令，访问局部变量表里面的某个局部变量
    public void visitLocalVariable(String name, String desc, String signature, Label start, Label end, int index);
    //指令，表示class方法体里面的字节码指令(如 IADD， ICONST_0, ARETRURN等字节码指令)
    public void visitXxxInsn();
    //如果方法体中有跳转指令，字节码指令中会出现label，所谓label可以近似看成行号的标记，指示跳转到的位置
    public void visitLabel(Label label);
    //记录当前栈帧状态，用于class文件加载时的校验
    public void visitFrame(int type, int nLocal, Object[] local, int nStack, Object[] stack);
    //指定当前方法的栈帧中，局部变量表和操作数栈的大小（java栈大小是javac之后就确定的）
    public void visitMaxs(int maxStack, int maxLocals);
}
```

#### 字节码基础

##### java类型描述符

|Java type  |  Type descriptor|
|---|---|
|boolean  |   Z|
|char  |  C|
|byte  |  B|
|short  |  S|
|int  |  I|
|float  |  F|
|long  |  J|
|double  |  D|
|Object  |  Ljava/lang/Object;|
|int[]  |  [I|
|Object[][]  |  [[Ljava/lang/Object;|

> **自定义引用类的表示: "L全限定名;"**
>
> 如  android.app.Activity 描述符为 "Landroid/app/Activity;"
>

 **方法描述符的表示法：**
 
(参数类型描述符)返回值描述符

```java
int getId(Object o);
//描述符为
(Ljava/lang/Object;)I
```

##### 局部变量表

顾名思义，存储当前方法中的局部变量，包括方法的入参，第一个是存放的是this

```java
public MethodVisitor visitMethod(int access, String name ,String desc, String signature, String[] Exceptions);
```

刚进入此方法时，局部变量表的槽位状态



|Slot Number  |value  |
|---|---|
|0  | this  |
|1  |  int access|
|2  |  String name|
|3  |  String desc|
|4  |  String signature|
|5  |  String[] Exceptions|

#### 实践一下

了解了ASM基础知识，下面来实现我们的demo

##### 先实现我们的gradle插件

```java
class PluginImpl implements Plugin<Project>{
  @Override
  void apply (Project project){
    ...
    // 调用自定义后的transform
    android.registerTransform(new InjectTransform());
    ...
  }
}

public class InjectTransform extends Transform{
  @Override
  public void transform((TransformInvocation transformInvocation)
            throws TransformException, InterruptedException, IOException  {
              ...
              //修改字节码
              transform(file);
              ...
  }  
}
```
可见gradle插件只是个工具，提供了接口，真正做工作的还是字节码操作。

##### ASM如何操作字节码

asm操作字节码的简单思路为(以修改方为例)：根据方法名、方法参数和返回值等定位位置，并替换成新的方法。

```java
public void transfrom(File file){
    ...
        ClassReader classReader = new ClassReader(new FileInputStream(file));
        ClassWriter classWriter = new ClassWriter(classReader, ClassWriter.COMPUTE_MAXS);

        MonitorClassVisitor monitorClassVisitor = new MonitorClassVisitor(Opcodes.ASM5, classWriter);

        classReader.accept(monitorClassVisitor, ClassReader.EXPAND_FRAMES);
        FileOutputStream fileOutputStream = new FileOutputStream(file);
        fileOutputStream.write(classWriter.toByteArray());
    ...
  }
```
```java
//操作字节码的具体实现类
public class MonitorClassVisitor extends ClassVisitor{
      @Override
      MethodVisitor visitMethod(int access, String name, String desc, String signature, String[] exceptions) {
        MethodVisitor methodVisitor;
        methodVisitor = super.visitMethod(access, name, desc, signature, exceptions);

        return new MonitorMethodVisitor(Opcodes.ASM5, methodVisitor, access, name, desc);
  }
}
```
```java
//修改替换方法的实现类
public class MonitorMethodVisitor extends AdviceAdapter{
    @Overrid
   void visitMethodInsn(int opcode, String owner, String name, String desc, boolean itf) {
     //将满足判断条件的方法修改成要替换的方法
        if ("openConnection".equals(name) && "()Ljava/net/URLConnection;".equals(desc)&&
        "java/net/HttpURLConnection".equals(owner)) {
            mv.visitMethodInsn(INVOKESTATIC, "com/monitor/Monitor", "hookOpenConnection",
            "(Ljava/net/URL;)Ljava/net/HttpURLConnection;", false)
        }else{
            super.visitMethodInsn(opcode, owner, name, desc, itf);
    }
}
```
编译查看.class文件，已经变成了``HttpURLConnectionconn=Monitor.hookOpenConnection(url);`` 成功替换了方法调用。


## 总结一下

#### 做了什么？

不知道方法在做什么，但是缺了点什么，想要加点内容进去还能神不知鬼不觉，该怎么办？

#### 怎么做的？

先让gradle找到这个方法，再让ASM去和字节码交流，达成共识，替换成我们的方法，当然不忘初衷的事就需要考验ASM的真诚了，如果修改了原方法的逻辑，说不定下一个方法就会发现找不到上一个方法的返回值。所以，多点真诚，少点bug。

#### 不足之处？

需要对ASM熟悉，分析需要hook的源码，适配不同框架，甚至对同一个框架不同版本也需要适配，比如okhttp2 和okhttp3。
