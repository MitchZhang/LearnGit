# Android SVG 调研
## 1.什么是SVG
- SVG 指可伸缩矢量图形 (Scalable Vector Graphics)
- SVG 使用 XML 格式定义矢量图形
- SVG 图像在放大或改变尺寸的情况下其图形质量不会有所损失

## 2.SVG(矢量图)的优点
- 相对于PNG,JPEG,GIF体积小
- 相对于JPG,图像放大或改变尺寸情况下图像质量不会损失变形

## 3.Android 对于SVG矢量图的支持
Android 5.0(Api 21)添加了对于矢量图的支持.VectorDrawable 和AnimatedVectorDrawable,一个是矢量图的显示，一个是对于矢量图动画的支持.
对于5.0以下兼容可以使用 Android support library 的 VectorDrawableCompat 和AnimatedVectorDrawableCompat。

```
Android 5.0 (API level 21) was the first version to officially support vector drawables with VectorDrawable and AnimatedVectorDrawable, but you can support older versions with the Android support library, which provides the VectorDrawableCompat and AnimatedVectorDrawableCompat classes.
```

- [Guides概论](https://developer.android.com/guide/topics/graphics/vector-drawable-resources)
- [VectorDrawable](https://developer.android.com/reference/android/graphics/drawable/VectorDrawable)
- [AnimatedVectorDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedVectorDrawable)

## 4.SVG如何转化成VectorDrawable
打开AndroidStudio,右键res/drawable,new 一个VectorAsset,如下图所示:
![image](https://i.imgur.com/C4cJlme.png)
如图,你可以点击Clip Art从Android官方库提取已经生成好的VectorDrawable,也可以点击Local file选取一个本地的SVG或者PSD,IDE会自动帮你转换.

### 4.1 转换之后的SVG
  三角形矢量图的SVG
```
<vector
    android:height="50dp"
    android:viewportHeight="300.0"
    android:viewportWidth="300.0"
    android:width="50dp"
    xmlns:android="http://schemas.android.com/apk/res/android">
    <group
        android:pivotX="150.0"
        android:pivotY="150.0">
        <path
            android:fillColor="@color/colorPrimary"
            android:pathData="M100,100 L200,100 L150,200 Z"
            />
    </group>
</vector>

android:height,width: 矢量图的宽高
android:viewportHeight,viewportWidth: 将50dp*50dp 按300*300分成坐标系
path: 描述这个矢量图SVG的值

```
如下图所示：

![image](https://i.imgur.com/PEkBlAr.png)
### 4.2 SVG语法

```
M = moveto 相当于 android Path 里的moveTo(),用于移动起始点 
L = lineto 相当于 android Path 里的lineTo()，用于画线 
H = horizontal lineto 用于画水平线 
V = vertical lineto 用于画竖直线 
C = curveto 相当于cubicTo(),三次贝塞尔曲线 
S = smooth curveto 同样三次贝塞尔曲线，更平滑 
Q = quadratic Belzier curve quadTo()，二次贝塞尔曲线 
T = smooth quadratic Belzier curveto 同样二次贝塞尔曲线，更平滑 
A = elliptical Arc 相当于arcTo()，用于画弧 
Z = closepath 相当于closeTo(),关闭path

大写代表绝对位置, 小写代表相对位置

```
## 5.如何让SVG动起来
SVG的动画,就是path的变化,一个path变为另外一个path,再配合延迟等属性动画即可达到动画效果.

### 5.1 Sample one: Path变化
对勾和叉叉的SVG之间切换

```
    // 对勾 path
        <path
            android:name="duigou"
            android:strokeWidth="0.5"
            android:strokeColor="#fffefe"
            android:strokeLineJoin="round"
            android:strokeLineCap="round"
            android:pathData="M 1,5 L3,8 M 3,8 L 8,2"/>
            
    //叉叉 path
       <path
            android:name="chacha"
            android:strokeWidth="0.5"
            android:strokeColor="#fffefe"
            android:strokeLineJoin="round"
            android:strokeLineCap="round"
            android:pathData="M 9,9 L 1,1 M 9,1 L 1,9"/>        
```
效果图如下:

![image](https://i.imgur.com/P5xTWzt.gif)

how to do:
-  1.编写AnimatedVectorDrawable的xml

```
ani_duigou.xml:
<animated-vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/svg_duigou">
    <target
        android:animation="@animator/duigou_change"
        android:name="duigou"/>
</animated-vector>

animated-vector 对应 AnimatedVectorDrawable
target 指具体对哪一个path或者group进行动画操作
animation 指对具体的动画操作类型

duigou_change.xml：
<?xml version="1.0" encoding="utf-8"?>
<objectAnimator
xmlns:android="http://schemas.android.com/apk/res/android"
android:duration="500"
android:propertyName="pathData"
android:valueType="pathType"
android:valueFrom="M 1,5 L3,8 M 3,8 L 8,2"
android:valueTo="M 9,9 L 1,1 M 9,1 L 1,9"/>

它的意思是,将对勾("M 1,5 L3,8 M 3,8 L 8,2")的路径在0.5s内转换成叉叉("M 9,9 L 1,1 M 9,1 L 1,9")

```
- 2.给ImageView设置动画

```
   <ImageView
            android:id="@+id/svg_triangle"
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:src="@drawable/ani_triangle"/>
```

- 3.执行动画

```
  ImageView triangle = (ImageView) view;
        if(triangle.getDrawable() instanceof Animatable){
            ((Animatable) triangle.getDrawable()).start();
        }
```

*加点旋转*:

```
<animated-vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/svg_duigou">
    <target
        android:animation="@animator/duigou_change"
        android:name="duigou"/>
    
    //path 变化后对整个group 进行旋转
    <target
        android:animation="@animator/animator_rotation"
        android:name="dgroup"/>
</animated-vector>
        
```
> 注意：不能直接对path进行旋转，否则会异常

效果图:

![image](https://i.imgur.com/ktHFX1h.gif)


## 5.2 Sample Two 裁剪路径做出动画
### 5.2.1 理解TrimPath系列属性
```
//官方解释
android:trimPathStart
The fraction of the path to trim from the start, in the range from 0 to 1. Default is 0.
android:trimPathEnd
The fraction of the path to trim from the end, in the range from 0 to 1. Default is 1.
android:trimPathOffset
Shift trim region (allows showed region to include the start and end), in the range from 0 to 1. Default is 0.

trimPathStart: 从开头开始裁剪路径,范围0-1,默认值为0.
trimPathEnd: 从尾部开始裁剪,范围0-1,默认为1.
trimPathOffset：移动开始裁剪的坐标点.
```

![image](https://i.imgur.com/R5KxaIR.png)

上图是原SVG和做出裁剪后的SVG的对比图:

- 1.trimPathStart=0.5  由Path起点开始裁剪50%
- 2.trimPathEnd=0.2 由Path终点裁剪到20%,共裁了整个Path的80%
- 3.将坐标点移动到50%的位置,开始裁剪
- 4.将坐标点移动到80%的位置，往前裁剪
- 5.将坐标点移动到80%的位置,往后始裁


### 5.2.2 一步画出一个图形
效果图如下:

![image](https://i.imgur.com/oT7l4hP.gif)

上面看起来像延迟一样的动画，其实是通过上述路径裁剪变换做出来的.动画的和AnimatedVectorDrawable如下图所示:

```

<animated-vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/svg_mutistar">
    
    //对多边形做Path变换处理
    <target
        android:animation="@animator/animator_delay"
        android:name="multi_line"/>

</animated-vector>


animator_delay.xml:

<objectAnimator
android:duration="3000"
android:propertyName="trimPathStart"
android:valueFrom="1"
android:valueTo="0"
android:valueType="floatType"
android:interpolator="@android:interpolator/linear"
xmlns:android="http://schemas.android.com/apk/res/android"/>

// 整个过程就是矢量图 trimPathStart=1 在3秒内向 trimPathStart=0 转换的过程,直观看起来就是一笔画完.
```

### 5.3 Sample Three animated-selector 设置path转换的过渡动画
效果图如下: 对勾变成叉之后,再次点击,变回勾.

![image](https://i.imgur.com/KkZDwnE.gif)

选中状态设置为勾,按下状态设置为叉


```
<?xml version="1.0" encoding="utf-8"?>
<animated-selector
    xmlns:android="http://schemas.android.com/apk/res/android">

    <item
        android:id="@+id/press"
        android:state_checked="true"
        android:drawable="@drawable/svg_duigou"/>

    <item
        android:id="@+id/normal"
        android:drawable="@drawable/svg_chacha"/>

    //从normal 到 press 状态这个过程中设置的动画
    <transition
        android:fromId="@+id/normal"
        android:toId="@+id/press"
        android:drawable="@drawable/ani_start_to_muti"/>
        
    //同理
    <transition
        android:fromId="@+id/press"
        android:toId="@+id/normal"
        android:drawable="@drawable/ani_muti_to_star"/>
        
</animated-selector>
```
> <transition>标签中设置的drawable 就是矢量图的动画




## 6.Path变换异常
比较复杂的矢量图之间，变换的时候有可能会出现以下异常,Can't morph from xxx,这是由于二个路径切换的时候不匹配.

```
Caused by: android.content.res.Resources$NotFoundException: Drawable com.example.mitch.svgsamples:drawable/ani_selector_one with resource ID #0x7f070059
08-09 15:51:07.869 27333-27333/com.example.mitch.svgsamples E/AndroidRuntime: Caused by: android.view.InflateException:  Can't morph from M793.49,949.18 L511.43,743.86 229.37,949.18l114.07,-335.98L63.45,407.88l335.99,0L511.43,73.97l111.99,333.91 335.99,0L679.42,613.2 793.49,949.18zM511.43,702.38l215.69,159.7 -85.03,-261.32 217.76,-159.7L598.54,441.06l-87.11,-263.39 -87.11,263.39L163,441.06 380.77,600.76l-85.03,261.32L511.43,702.38z to M 50.0,90.0 L 82.9193546357,27.2774101308 L 12.5993502926,35.8158045183 L 59.5726265715,88.837672697 L 76.5249063296,20.0595700732 L 10.2916450361,45.1785327898 L 68.5889268818,85.4182410261 L 68.5889268818,14.5817589739 L 10.2916450361,54.8214672102 L 76.5249063296,79.9404299268 L 59.5726265715,11.162327303 L 12.5993502926,64.1841954817 L 82.9193546357,72.7225898692 L 50.0,10.0 L 17.0806453643,72.7225898692 L 87.4006497074,64.1841954817 L 40.4273734285,11.162327303 L 23.4750936704,79.9404299268 L 89.7083549639,54.8214672102 L 31.4110731182,14.5817589739 L 31.4110731182,85.4182410261 L 89.7083549639,45.1785327898 L 23.4750936704,20.0595700732 L 40.4273734285,88.837672697 L 87.4006497074,35.8158045183 L 17.0806453643,27.2774101308 L 50.0,90.0Z
        at android.animation.AnimatorInflater.getPVH(AnimatorInflater.java:308)
        at android.animation.AnimatorInflater.parseAnimatorFromTypeArray(AnimatorInflater.java:422)
        at android.animation.AnimatorInflater.loadAnimator(AnimatorInflater.java:1053)
        at android.animation.AnimatorInflater.loadObjectAnimator(AnimatorInflater.java:1011)
        at android.animation.AnimatorInflater.createAnimatorFromXml(AnimatorInflater.java:667)
        at android.animation.AnimatorInflater.createAnimatorFromXml(AnimatorInflater.java:642)
        at android.animation.AnimatorInflater.loadAnimator(AnimatorInflater.java:126)
```
解决方式:
- [vectalign](https://github.com/bonnyfone/vectalign) github 开源比对库,比对path之后,分别粘贴回去,即可兼容.
- java -jar  vectalign.jar  打开对应jar 图形界面进行操作

![image](https://i.imgur.com/MB4vjUP.png)

- [Demo下载地址](https://github.com/MitchZhang/SVGSample)