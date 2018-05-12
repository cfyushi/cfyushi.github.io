# level-list(级别列表)

管理大量备选可绘制对象的可绘制对象，每个可绘制对象都分配有最大的备选数量。使用 `setLevel()` 设置可绘制对象的级别值会加载级别列表中 `android:maxLevel` 值大于或等于传递到方法的值的可绘制对象资源。

##语法

```
<?xml version="1.0" encoding="utf-8"?>
<level-list
    xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:drawable="@drawable/drawable_resource"
        android:maxLevel="integer"
        android:minLevel="integer" />
</level-list>
```

**<level-list>**

这必须是根元素。包含一个或多个 `<item>` 元素。

**<item>** 

属性：

- android:drawable

  *可绘制对象资源*。**必备**。引用要插入的可绘制对象资源。

- android:maxLevel

  *整型*。此项目允许的最高级别。

- android:minLeve

  *整型*。此项目允许的最低级别。

## 应用

在Imageview和Button上应用，注意差异

* 创建level_list_test.xml

```
<?xml version="1.0" encoding="utf-8"?>
<level-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:drawable="@android:color/holo_green_dark"
        android:maxLevel="100"
        android:minLevel="70" />
    <item
        android:drawable="@android:color/holo_red_dark"
        android:maxLevel="69"
        android:minLevel="40" />
    <item
        android:drawable="@android:color/holo_blue_dark"
        android:maxLevel="39"
        android:minLevel="0" />
</level-list>
```

* 在Imageview中使用src，在Button中使用background

```
    <ImageView
        android:id="@+id/iv_level_list"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="left"
        android:src="@drawable/level_list_test" />
    <Button
        android:id="@+id/btn_level_list"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="left"
        android:background="@drawable/level_list_test" />
```

* setLevel

```
  private void setTestLevel(int level){
        //Button上的使用
        LevelListDrawable levelListDrawable = (LevelListDrawable)mBtnTest.getBackground();
        levelListDrawable.setLevel(level);
        
        //ImageView上的使用
        mIvTest.setImageLevel(level);
    }
```

# selector(状态列表)



qqq





#layer-list(图层列表)



## 引用

https://developer.android.google.cn/guide/topics/resources/drawable-resource#LevelList