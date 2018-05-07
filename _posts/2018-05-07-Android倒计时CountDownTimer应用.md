

CountDownTimer是Android系统提供的倒计时库，应用和源码都很简单。

* 在纯demo情况下，间隔20ms时无误差

## 应用

* 创建对象

```
private CountDownTimer mCountDownTimer;
mCountDownTimer = new CountDownTimer(60_000/*总时间*/, 1_000/*间隔时间*/) {
                    @Override
                    public void onTick(long millisUntilFinished) {
                        int second = (int) (millisUntilFinished / 1000);
                    }

                    @Override
                    public void onFinish() {}
                };
```

* 开始倒计时

```
mCountDownTimer.start();
```

* 停止，在activity、fragment销毁或者需要时调用

```
mCountDownTimer.cancel();
```

## 源码

源码很简单，利用Handler

```
private Handler mHandler = new Handler() {

    @Override
    public void handleMessage(Message msg) {

        synchronized (CountDownTimer.this) {
            if (mCancelled) {
                return;
            }

            final long millisLeft = mStopTimeInFuture - SystemClock.elapsedRealtime();

            if (millisLeft <= 0) {
                onFinish();
            } else {
                long lastTickStart = SystemClock.elapsedRealtime();
                onTick(millisLeft);

                // take into account user's onTick taking time to execute
                //考虑用户的onTick执行时间
                long lastTickDuration = SystemClock.elapsedRealtime() - lastTickStart;
                long delay;

                if (millisLeft < mCountdownInterval) {
                    // just delay until done
                    delay = millisLeft - lastTickDuration;

                    // special case: user's onTick took more than interval to
                    // complete, trigger onFinish without delay
                    //特殊情况:用户的onTick事件超出了时间间隔
                    //完成，立即触发
                    if (delay < 0) delay = 0;
                } else {
                    delay = mCountdownInterval - lastTickDuration;

                    // special case: user's onTick took more than interval to
                    // complete, skip to next interval
                    //特殊情况:用户的onTick事件超出了时间间隔
                    //完成，立即触发
                    while (delay < 0) delay += mCountdownInterval;
                }

                sendMessageDelayed(obtainMessage(MSG), delay);
            }
        }
    }
};
```