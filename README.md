# Lottie 安卓版, [iOS版](https://github.com/CRAnimation/lottie-ios), and [React Native版](https://github.com/airbnb/lottie-react-native)  
<br>
<a href='https://play.google.com/store/apps/details?id=com.airbnb.lottie'><img alt='Get it on Google Play' src='https://play.google.com/intl/en_us/badges/images/generic/en_badge_web_generic.png' height="80px"/></a> 

 ***这是Lottie项目说明的中文版，由[BigXiXi](https://github.com/bigxixi)翻译, 原文地址：https://github.com/airbnb/lottie-android/<br>
Lottie-iOS版的翻译[点这里](https://github.com/CRAnimation/lottie-ios)，由[CRAnimation团队](https://github.com/CRAnimation)提供。<br>
文中After Effects术语参考自官方中文版AE。***   


Lottie 是一个用于 Android 和 iOS 的代码库，可以解析用 [Adobe After Effects](http://www.adobe.com/products/aftereffects.html) 制作动画后通过 [Bodymovin 插件](https://github.com/bodymovin/bodymovin) 导出的JSON数据文件并在移动端原生渲染!

前所未有地, 设计师们终于可以创造 **并传递** 优美的动画给工程师们，再也不用让工程师痛苦地手工还原动画。都说一图胜千言，现在我们有千言万语:

![Example1](https://raw.githubusercontent.com/airbnb/lottie-android/master/gifs/Example1.gif)


![Example2](https://raw.githubusercontent.com/airbnb/lottie-android/master/gifs/Example2.gif)


![Example3](https://raw.githubusercontent.com/airbnb/lottie-android/master/gifs/Example3.gif)


![Community](https://raw.githubusercontent.com/airbnb/lottie-android/master/gifs/Community%202_3.gif)


![Example4](https://raw.githubusercontent.com/airbnb/lottie-android/master/gifs/Example4.gif)


所有这些动画都是在 After Effects 里创作的, 然后用 Bodymovin 插件导出在设备本地直接渲染的，没做任何附加的修改。

[Bodymovin](https://github.com/bodymovin/bodymovin) 是一款由 Hernan·Torrisi 编写的 After Effects 插件，用于将 After effects 动画导出为 JSON 数据，它包含一个JavaScript写的可用于web的播放器（译者注：这个播放器直接读取JSON数据来播放动画，可以直接用来做网页，这个是bodymovin自带的功能）。我们在他的大作的基础上把插件的功能应用于Android、iOS和React Native。

更多细节可以参阅我们的[博文](http://airbnb.design/introducing-lottie/)。
也可以在推特上和我们互动@ ([gpeal8](https://twitter.com/gpeal8)) 或者给我们写邮件，地址是 lottie@airbnb.com。

## 其他平台
 * [Web](https://github.com/bodymovin/bodymovin)
 * [Xamarin](https://github.com/martijn00/LottieXamarin)
 * [NativeScript](https://github.com/bradmartin/nativescript-lottie)
 * [Appcelerator Titanium](https://github.com/m1ga/ti.animation)

## 演示APP

你可以自己编译演示APP或者在 Google Play 下载[Play Store地址](https://play.google.com/store/apps/details?id=com.airbnb.lottie)。这款演示APP包含了一些自带的动画例子，当然你也可以从手机本地存储或者网络（通过网盘或URL）上加载自己的动画。


## 下载

Lottie 仅支持用 Gradle 构建配置, 请将依赖项添加到项目 `build.gradle` 文件中：

### 发行版
```groovy
dependencies {  
  compile 'com.airbnb.android:lottie:1.5.3'
}
```
### Beta版
```groovy
dependencies {  
  compile 'com.airbnb.android:lottie:2.0.0-beta1'
}
```

## 你的项目中使用了Lottie吗?

如果你在项目里使用了Lottie，可以通过邮件告诉我们（lottie@airbnb.com），我们将很快建立一个页面呈现来自全球的真实用户案例。  

## 如何使用Lottie
Lottie 支持ICS (API 14) 及以上版本。
最简单的用法是使用 LottieAnimationView:

```xml
<com.airbnb.lottie.LottieAnimationView
        android:id="@+id/animation_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:lottie_fileName="hello-world.json"
        app:lottie_loop="true"
        app:lottie_autoPlay="true" />
```

或者你也可以通过其他程序化的手段来加载。  
比如读取在 app/src/main/assets 中的JSON文件:  
```java
LottieAnimationView animationView = (LottieAnimationView) findViewById(R.id.animation_view);
animationView.setAnimation("hello-world.json");
animationView.loop(true);
```
这个方法可以在后台异步加载动画文件，并在加载完成后开始渲染动画。 

如果你想重用一个动画，例如用在一个列表中的每个条目中或者加载自网络请求的JSONObject：   
```java
 LottieAnimationView animationView = (LottieAnimationView) findViewById(R.id.animation_view);
 ...
 Cancellable compositionCancellable = LottieComposition.Factory.fromJson(getResources(), jsonObject, (composition) -> {
     animationView.setComposition(composition);
     animationView.playAnimation();
 });

 // 可以用cancel()方法来停止composition的异步加载
 // compositionCancellable.cancel();
```

然后你可以控制这个动画或者给他加个监听：  
```java
animationView.addAnimatorUpdateListener((animation) -> {
    //这里可添加自定义功能
});
animationView.playAnimation();
...
if (animationView.isAnimating()) {
    //这里可添加自定义功能
}
...
animationView.setProgress(0.5f);
...
// 自定义动画速度或时长
ValueAnimator animator = ValueAnimator.ofFloat(0f, 1f)
    .setDuration(500);
animator.addUpdateListener(animation -> {
    animationView.setProgress(animation.getAnimatedValue());
});
animator.start();
...
animationView.cancelAnimation();
```

本质上说， `LottieAnimationView` 使用 `LottieDrawable` 来渲染动画。如有需要，你也可以直接使用 drawable：  

```java
LottieDrawable drawable = new LottieDrawable();
LottieComposition.Factory.fromAssetFileName(getContext(), "hello-world.json", (composition) -> {
    drawable.setComposition(composition);
});
```
如果你的动画需要频繁重用， `LottieAnimationView` 自带一个可选的缓存机制 —— `LottieAnimationView#setAnimation(String, CacheStrategy)`。 `CacheStrategy` 参数的可选项是 `Strong`（强）, `Weak`（弱）, or `None`（无）， 用以设定 `LottieAnimationView` 对已加载和解析的动画的引用强弱程度。  
 
 
### 图像资源支持  
你也可以对图片资源做动画，此时用到的图片需存放在asset目录下的一个子目录中。只需调用`LottieAnimationView` 或 `LottieDrawable` 中的 `setImageAssetsFolder` 加载asset目录下的相对目录，并保持图片资源的文件名和从bodymovin中导出时一致（格式为 img_#.png/jpg ...）。
如果你是直接使用 `LottieDrawable` ，完成后需用 `recycleBitmaps` 方法来释放。

如果你需要以网络下载或者其他方式提供图片资源，可以用一个delegate来实现： 
 ```java
animationView.setImageAssetDelegate(new ImageAssetDelegate() {
          @Override public Bitmap fetchBitmap(LottieImageAsset asset) {
            getBitmap(asset);
          }
        });
```

## 支持的 After Effects 动画类型  
译者注：Lottie目前**不支持**的AE动效类型很多，例如常用的3D图层变换、几乎所有效果器[effects]、图层样式[layer styles]、叠加模式[blending mode]、渐变[gradient ramp/gradient fill...]（好像正在加入支持，期待更新）、任意表达式[expressions]、径向擦除[radius wipe]等。。。<br>
在用AE制作动画的时候一定要看清楚能否拆解为以下支持的类型。  
### 预合成[Pre-composition]

---

### 关键帧插值[Keyframe Interpolation]

---

* 线性插值[Linear Interpolation]

* 贝塞尔曲线插值[Bezier Interpolation]

* 定格插值[Hold Interpolation]

* 漂浮穿梭时间[Rove Across Time]

* 空间贝塞尔曲线[Spatial Bezier]

### 固态层[Solids]

---

* 锚点变换[Transform Anchor Point]

* 位置变换[Transform Position]

* 缩放变换[Transform Scale]

* 旋转变换[Transform Rotation]

* 不透明度变换[Transform Opacity]

### 蒙版[Masks]

---

* 蒙版路径[Path]

* 蒙版不透明度[Opacity]

* 多蒙版混合模式（相加、相减、反转）[Multiple Masks (additive, subtractive, inverted)]

### 轨道遮罩[Track Mattes]

---

* Alpha 遮罩[Alpha Matte]

### 父级[Parenting]

---

* 多个父级[Multiple Parenting]

* 空对象[Nulls]

### 形状图层[Shape Layers]

---

* 矩形（所有属性）[Rectangle (All properties)]

* 椭圆（所有属性）[Ellipse (All properties)]

* 多边星形（所有属性）[Polystar (All properties)]

* 多边形（所有属性。点个数必须为整数。）[Polygon (All properties. Integer point values only.)]

* 路径变换（所有属性）[Path (All properties)]

* 锚点变换[Anchor Point]

* 位置变换[Position]

* 缩放变换[Scale]

* 旋转变换[Rotation]

* 不透明度[Opacity]

* 形状组变换（锚点、位置、缩放……）[Group Transforms (Anchor point, position, scale etc)]

* 一个形状组可包含多个形状 [Multiple paths in one group]

* 合并路径（默认是关闭的，必须用enableMergePathsForKitKatAndAbove方法专门开启）[Merge paths (off by default and must be explicitly enabled with enableMergePathsForKitKatAndAbove)]

#### 形状图层描边 [Stroke (shape layer)]

---

* 描边颜色[Stroke Color]

* 描边不透明度[Stroke Opacity]

* 描边宽度[Stroke Width]

* 描边端点[Line Cap]

* 虚线[Dashes]

#### 形状图层填充[Fill (shape layer)]

---

* 填充颜色[Fill Color]

* 填充不透明度[Fill Opacity]

#### 修剪路径[Trim Paths (shape layer)]

---

* 修剪路径开始[Trim Paths Start]

* 修剪路径结束[Trim Paths End]

* 修剪路径偏移[Trim Paths Offset]

## 性能和内存占用
1. 如果动画合成中没有遮罩或者蒙版，性能和内存开销会非常棒。此时没有位图生成，大部分操作都是简单的canvas画布操作。  
2. 如果合成中存在遮罩或者蒙版, 动画将会使用脱屏缓冲区（offscreen buffer），性能将会受到影响。
3. 如果你在一个列表中使用动画, 我们建议你配置 LottieAnimationView.setAnimation(String, CacheStrategy) 的第二个参数——缓存策略，这样动画就不必每次都反序列化。 

## 来试试吧
克隆这个库并运行LottieSample模块，你将能看到很多示例动画。 动画的JSON数据文件存放在 [LottieSample/src/main/assets](https://github.com/airbnb/lottie-android/tree/master/LottieSample/src/main/assets) ，原始的AE工程文件存放在 [/After Effects Samples](https://github.com/airbnb/lottie-android/tree/master/After%20Effects%20Samples)

示例APP同样可以从url或本地设备加载并渲染JSON文件（比如downloads文件夹或者sdcard）。
 
## 其他方案
1. 手工编写动画代码。 同时为Android和iOS平台编写动画代码将会耗费巨大的难以预估的设计和开发时间。甚至在花费了如此多时间后仍无法保证可以完美还原动画。
2. [Facebook Keyframes](https://github.com/facebookincubator/Keyframes). Keyframes是Facebook出品的一个优秀的动画代码库。然而, Keyframes 支持的AE动画特性较少，例如Lottie所支持的遮罩、蒙版、路径修建、虚线等等它都没有提供支持。 
2. Gifs动画。 Gifs占用了数倍于bodymovin导出的JSON文件的大小，而且只能在一个固定的尺寸渲染无法放大适配高分辨率屏幕。（译者注：gif的问题还有颜色数最高只有256色导致的画质不高，画面中的渐变容易出现断层，如果是带透明通道的gif几乎都会产出毛边。）
3. Png图像序列。Png图像序列的效果甚至比gif还要糟糕，因为它的体积可能达到json的几十倍而且同样无法放大。

## 为什么叫做Lottie?
Lottie是以德国剪影动画先驱Lotte Reiniger（洛特·雷妮格）的名字命名的。 她最出名的作品是《阿基米德王子历险记》 (1926) – 世界上第一部长篇动画电影。 比华尔特·迪士尼的长篇动画电影——《白雪公主与七个小矮人》 (1937) 还要早了10年。  
[洛特·雷妮格的艺术](https://www.youtube.com/watch?v=LvU55CUw5Ck&feature=youtu.be)

## 为Lottie做贡献
我们非常欢迎您为Lottie贡献力量， 只需提交一个Pull Request并附上对你所做改进的描述。  
Lottie 使用 [Facebook screenshot tests for Android](https://github.com/facebook/screenshot-tests-for-android)（Facebook出品的安卓快照测试工具） 来检测像素级的变动。 请在发起PR前运行 `./gradlew --daemon recordMode screenshotTests` 来确保没有产生破坏性的改动。请使用模拟器运行搭载Lollipop系统的Nexus 5来做这个测试。如果快照有变动将会出现在你的git diff结果中。

如果你想加入更多的JSON文件和快照测试, 可以把测试加入到 `LottieTest`。 

## 有问题或者功能需求？
任何问题欢迎到github的issues页面提出。 如果你的After Effect文件导出的动画无效, 也请将AE工程文件作为附件粘贴到你的issue里。没有原始文件的情况下我们也很难诊断问题。
