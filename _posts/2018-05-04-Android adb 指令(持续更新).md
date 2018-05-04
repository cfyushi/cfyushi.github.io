针对开发过程中常用的adb指令进行记录

## 获取屏幕分辨率

指令：adb shell dumpsys window displays

输入上述指令会出现：

```
Display: mDisplayId=0
    init=1440x2560 640dpi cur=1440x2560 app=1440x2560 rng=1440x1344-2560x2464
    deferred=false mLayoutNeeded=false
```

