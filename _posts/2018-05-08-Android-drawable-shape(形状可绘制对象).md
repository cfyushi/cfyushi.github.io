##官方示例

* 文件位置，res/drawable/*filename*.xml，文件名用作资源 ID


* 指向 GradientDrawable 的资源指针

* 资源引用：

  在 Java 中：R.drawable.*filename*
  在 XML 中：@[*package*:]drawable/*filename*

* 语法

```
<?xml version="1.0" encoding="utf-8"?>
<shape
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape=["rectangle" | "oval" | "line" | "ring"] >
    <corners
        android:radius="integer"
        android:topLeftRadius="integer"
        android:topRightRadius="integer"
        android:bottomLeftRadius="integer"
        android:bottomRightRadius="integer" />
    <gradient
        android:angle="integer"
        android:centerX="float"
        android:centerY="float"
        android:centerColor="integer"
        android:endColor="color"
        android:gradientRadius="integer"
        android:startColor="color"
        android:type=["linear" | "radial" | "sweep"]
        android:useLevel=["true" | "false"] />
    <padding
        android:left="integer"
        android:top="integer"
        android:right="integer"
        android:bottom="integer" />
    <size
        android:width="integer"
        android:height="integer" />
    <solid
        android:color="color" />
    <stroke
        android:width="integer"
        android:color="color"
        android:dashWidth="integer"
        android:dashGap="integer" />
</shape>
```

## shape属性

**xmlns:android** 

字符串串。必备。定义 XML 命名空间，其必须是 "http://schemas.android.com/apk/res/android"。

**android:shape**

关键词，定义形状的类型，有效值为：

| 值          | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| "rectangle" | 填充包含视图的矩形。这是默认形状。                           |
| "oval"      | 适应包含视图尺⼨寸的椭圆形状。                               |
| "line"      | 跨越包含视图宽度的⽔水平线。此形状需要 <stroke> 元素定义线宽。 |
| "ring"      | 环形                                                         |

仅当 android:shape="ring" 如下时才使⽤用以下属性:

**android:innerRadius** 

尺寸。环内部(中间的孔)的半径，以尺⼨寸值或尺⼨寸资源表示。

**android:innerRadiusRatio** 

浮点型。环内部的半径，以环宽度的⽐比率表示。例如，如果android:innerRadiusRatio="5"，则内半径等于环宽度除以 5。此值被android:innerRadius 覆盖。默认值为 9。

**android:thickness** 

尺⼨。环的厚度，以尺⼨寸值或尺⼨寸资源表示。

**android:thicknessRatio**

浮点型。环的厚度，表示为环宽度的⽐比率。例例如，如果 android:thicknessRatio="2"，则厚度等于环宽度除以 2。此值被android:innerRadius 覆盖。默认值为 3。

**android:useLevel**
布尔值。如果这⽤用作 LevelListDrawable (https://developer.android.com/reference/android/graphics/drawable/LevelListDrawable.html)，则此值为“true”。这通常应为“false”，否则形状不不会显示。



## corners属性

为形状产⽣生圆⻆角。仅当形状为矩形时适⽤

**android:topLeftRadius**

尺⼨寸。左上⻆角的半径，以尺⼨寸值或尺⼨寸资源表示。

**android:topRightRadius**

尺⼨寸。右上⻆角的半径，以尺⼨寸值或尺⼨寸资源 表示。

**android:bottomLeftRadius**

尺⼨寸。左下⻆角的半径，以尺⼨寸值或尺⼨寸资源表示。

**android:bottomRightRadius**

尺⼨寸。右下⻆角的半径，以尺⼨寸值或尺⼨寸资源表示。

**必须为每个角提供大于 1 的角半径，否则无法产生圆角。如果希望特定角不要倒圆角，解决方法是使用 android:radius 设置大于 1 的默认角半径，然后使用实际所需的值替换每个角，为不希望倒圆角的角提供零（“0dp”）。**



## gradient属性

指定形状的渐变颜色

**android:angle**

*整型*。渐变的角度（度）。0 为从左到右，90 为从上到上。**必须是 45 的倍数。默认值为 0。仅仅在 `android:type="linear"` 时适用**

**android:centerX**

*浮点型*。渐变中心的相对 X 轴位置 (0 - 1.0)。

**android:centerY**

*浮点型*。渐变中心的相对 Y 轴位置 (0 - 1.0)。

**centerX，centerY 在"linear"时表示centerColor的位置，在"radia"时表示圆形中心点的位置的位置，在"sweep"时表示扫描中心点的位置的位置。**

**android:centerColor**

*颜色*。起始颜色与结束颜色之间的可选颜色，以十六进制值或[颜色资源](https://developer.android.google.cn/guide/topics/resources/more-resources.html#Color)表示。不指定时，系统会根据startColor和endColor取中值。

**android:endColor**

*颜色*。结束颜色，表示为十六进制值或[颜色资源](https://developer.android.google.cn/guide/topics/resources/more-resources.html#Color)。

**android:gradientRadius**

*浮点型*。渐变的半径。仅在 `android:type="radial"` 时适用。

**android:startColor**

*颜色*。起始颜色，表示为十六进制值或[颜色资源](https://developer.android.google.cn/guide/topics/resources/more-resources.html#Color)。

**android:type**

*关键字*。要应用的渐变图案的类型。有效值为：

| 值       | 说明                         |
| -------- | ---------------------------- |
| "linear" | 线性渐变。这是默认值。       |
| "radial" | 径向渐变。起始颜色为中心颜色 |
| "sweep"  | 流线型渐变，扫描渐变         |

**android:useLevel**

*布尔值*。如果这用作 `LevelListDrawable`，则此值为“true”。



##solid属性

用于填充形状的纯色。 

属性：

**android:color**

*颜色*。应用于形状的颜色，以十六进制值或[颜色资源](https://developer.android.google.cn/guide/topics/resources/more-resources.html#Color)表示

##stroke属性

形状的描边 

**android:width**

尺寸。线宽，以尺寸值或尺寸资源表示。

**android:color**

颜色。线的颜色，表示为十六进制值或颜色资源。

**android:dashGap**

尺寸。短划线的间距，以尺寸值或尺寸资源表示。仅在设置了 android:dashWidth 时有效。

**android:dashWidth**

尺寸。每个短划线的大小，以尺寸值或尺寸资源表示。仅在设置了 android:dashGap 时有效。

**虚线不显示的方法**

1. 在代码中解决：view.setLayerType(View.LAYER_TYPE_SOFTWARE, null);把这句加点相应的代码中；
2. 在 AndroidManifest.xml中解决，android:hardwareAccelerated="false" 加点相应的Activity处即可。

##padding属性

要应用到包含视图元素的内边距（这会填充视图内容的位置，而非形状）

**android:left**

*尺寸*。左内边距，表示为尺寸值或[尺寸资源](https://developer.android.google.cn/guide/topics/resources/more-resources.html#Dimension)

**android:top**

*尺寸*。上内边距，表示为尺寸值或[尺寸资源](https://developer.android.google.cn/guide/topics/resources/more-resources.html#Dimension)

**android:right**

*尺寸*。右内边距，表示为尺寸值或[尺寸资源](https://developer.android.google.cn/guide/topics/resources/more-resources.html#Dimension)

**android:bottom**

*尺寸*。下内边距，表示为尺寸值或[尺寸资源](https://developer.android.google.cn/guide/topics/resources/more-resources.html#Dimension)

**直接在shape中使用padding无效，应该:**

```
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:drawable="@drawable/shape_rectangle_a"
        android:top="40dp" />
</layer-list>
```



##size属性

形状的大小，受android:layout_width和android:layout_height的限制

**android:height**

*尺寸*。形状的高度，表示为尺寸值或[尺寸资源](https://developer.android.google.cn/guide/topics/resources/more-resources.html#Dimension)

**android:width**

*尺寸*。形状的宽度，表示为尺寸值或[尺寸资源](https://developer.android.google.cn/guide/topics/resources/more-resources.html#Dimension)

**注**：默认情况下，形状按照此处定义的尺寸按比例缩放至容器视图的大小。在 `ImageView` 中使用形状时，可通过将 [`android:scaleType`](https://developer.android.google.cn/reference/android/widget/ImageView.html#attr_android:scaleType) 设置为 `"center"` 来限制缩放。







