## 多种屏幕密度

| 屏幕密度 | 对应尺寸及说明                                               | 对应目录                    |
| :------- | :----------------------------------------------------------- | :-------------------------- |
| ldpi     | 低密度屏幕；约为 120dpi                                      | drawable-ldpi               |
| mdpi     | 中等密度（传统 HVGA）屏幕；约为 160dpi                       | drawable-mdpi<br />drawable |
| hdpi     | 高密度屏幕；约为 240dpi                                      | drawable-hdpi               |
| xhdpi    | 超高密度屏幕；约为 320dpi                                    | drawable-xhdpi              |
| xxhdpi   | 超超高密度屏幕；约为 480dpi                                  | drawable-xxhdpi             |
| xxxhdpi  | 超超超高密度屏幕；约为 640dpi                                | drawable-xxxhdpi            |
| nodpi    | 它可用于您不希望缩放以匹配设备密度的位图资源                 | drawable-nodpi              |
| tvdpi    | 密度介于 mdpi 和 hdpi 之间的屏幕；约为 213dpi。<br />它并不是“主要”密度组， 主要用于电视， | drawable-tvdpi              |
| anydpi   | 此限定符适合所有屏幕密度，其优先级高于其他限定符。<br />这对于矢量可绘制对象很有用 | drawable-anydpi             |

**系统默认生成的文件夹drawable相当于mdpi**



## dpi计算

dpi值=对角线/屏幕尺寸，对角线可通过长宽计算



## 如何查找最佳资源

1. 查找与屏幕密度最匹配的目录
2. 没有最匹配的目录或者目录中没有相应资源，向更高密度的目录查找，如果找到资源做缩小处理
3. 往更高密度目录均为查找到，查找drawable-nodpi，不缩放资源
4. 仍没有，往低密度目录查找，如果找到资源做放大处理



## 缩放比例系数

| 密度    | 放大倍数 |
| ------- | -------- |
| ldpi    | 0.75     |
| mdpi    | 1        |
| hdpi    | 1.5      |
| xhdpi   | 2        |
| xxhdpi  | 3        |
| xxxhdpi | 4        |



## 最佳实践

**目前Android手机基本上都是xhdpi、xxhdpi、xxxhdpi，一般情况下我们可以全部放在drawable-xxhdpi目录下。UI经常切3套(@x@2x@3x)，我们可以只把@3x图片放在drawable-xxhdpi目录下**



# mipmap

mipmap是存放launch icon的。

| 密度              | 建议尺寸 | 对应目录       |
| ----------------- | -------- | -------------- |
| mdpi(中)          | 48*48    | mipmap-mdpi    |
| hdpi(高)          | 72*72    | mipmap-hdpi    |
| xhdpi(超高)       | 96*96    | mipmap-xhdpi   |
| xxhdpi(超超高)    | 144*144  | mipmap-xxhdpi  |
| xxxhdpi(超超超高) | 192*192  | mipmap-xxxhdpi |