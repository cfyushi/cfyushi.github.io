翻译Android开发者网站的文章。

原味地址：https://developer.android.com/topic/libraries/architecture/lifecycle.html

# Handling Lifecycles with Lifecycle-Aware Components

##使用Lifecycle-Aware处理生命周期

Lifecycle-aware components perform actions in response to a change in the lifecycle status of another component, such as activities and fragments. These components help you produce better-organized, and often lighter-weight code, that is easier to maintain.

**Lifecycle-aware感知组件在响应另一个组件(例如activities和fragments)的生命周期状态的变化时执行操作。这些组件可以帮助您生成更有组织的、通常更轻量级的代码，这样更容易维护。**

*A common pattern is to implement the actions of the dependent components in the lifecycle methods of activities and fragments. However, this pattern leads to a poor organization of the code and to the proliferation of errors. By using lifecycle-aware components, you can move the code of dependent components out of the lifecycle methods and into the components themselves.*

**一个常见的模式是在activities和 fragments的生命周期方法中实现依赖组件的操作。然而，这种模式导致了代码的组织不完善和错误的扩散。通过使用lifecycle-aware组件，您可以将依赖组件的代码从生命周期方法中转移到组件本身中。**

The [`android.arch.lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/package-summary.html) package provides classes and interfaces that let you build *lifecycle-aware* components—which are components that can automatically adjust their behavior based on the current lifecycle state of an activity or fragment.

**包 [`android.arch.lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/package-summary.html) 提供了类和接口，让您构建lifecycle-aware组件——这些组件可以根据activit或 fragment的当前生命周期状态自动调整其行为。**

```
Note: To import android.arch.lifecycle into your Android project, see adding components to your project.
```

```
注:如何导入android.arch.lifecycle到在您的Android项目中，请参见添加组件到您的项目中。
```



Most of the app components that are defined in the Android Framework have lifecycles attached to them. Lifecycles are managed by the operating system or the framework code running in your process. They are core to how Android works and your application must respect them. Not doing so may trigger memory leaks or even application crashes.

**Android框架中定义的大多数应用程序组件都有生命周期。生命周期由操作系统或运行在流程中的框架代码来管理。它们是Android工作的核心，你的应用程序必须尊重它们。不这样做可能会触发内存泄漏，甚至应用程序崩溃。**

Imagine we have an activity that shows the device location on the screen. A common implementation might be like the following:

**假设我们有一个activity，它显示屏幕上的设备位置。一个常见的实现可能如下所示:**

```
class MyLocationListener {
    public MyLocationListener(Context context, Callback callback) {
        // ...
    }

    void start() {
        // connect to system location service
    }

    void stop() {
        // disconnect from system location service
    }
}

class MyActivity extends AppCompatActivity {
    private MyLocationListener myLocationListener;

    @Override
    public void onCreate(...) {
        myLocationListener = new MyLocationListener(this, (location) -> {
            // update UI
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        myLocationListener.start();
        // manage other components that need to respond
        // to the activity lifecycle
    }

    @Override
    public void onStop() {
        super.onStop();
        myLocationListener.stop();
        // manage other components that need to respond
        // to the activity lifecycle
    }
}
```

Even though this sample looks fine, in a real app, you end up having too many calls that manage the UI and other components in response to the current state of the lifecycle. Managing multiple components places a considerable amount of code in lifecycle methods, such as `onStart()` and `onStop()`, which makes them difficult to maintain.

尽管这个示例看起来很好，但在一个真正的应用程序中，您最终会有太多的调用来管理UI和其他组件以响应生命周期的当前状态。管理多个组件在生命周期方法中放置了大量的代码，例如onStart()和onStop()，这使得它们难以维护。

Moreover, there's no guarantee that the component starts before the activity or fragment is stopped. This is especially true if we need to perform a long-running operation, such as some configuration check in `onStart()`. This can cause a race condition where the `onStop()` method finishes before the `onStart()`, keeping the component alive longer than it's needed.

此外，不能保证组件在activity和fragment停止之前启动。如果我们需要执行长时间运行的操作，比如onStart()中的一些配置检查，这一点尤其正确。这可能导致一个race条件，其中onStop()方法在onStart()之前完成，使组件的存活时间超过所需时间。

```
class MyActivity extends AppCompatActivity {
    private MyLocationListener myLocationListener;

    public void onCreate(...) {
        myLocationListener = new MyLocationListener(this, location -> {
            // update UI
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        Util.checkUserStatus(result -> {
            // what if this callback is invoked AFTER activity is stopped?
            if (result) {
                myLocationListener.start();
            }
        });
    }

    @Override
    public void onStop() {
        super.onStop();
        myLocationListener.stop();
    }
}
```

The [`android.arch.lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/package-summary.html) package provides classes and interfaces that help you tackle these problems in a resilient and isolated way.

包 [`android.arch.lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/package-summary.html) 。生命周期包提供了类和接口，帮助您以一种弹性和隔离的方式解决这些问题。

## Lifecycle

------

[`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) is a class that holds the information about the lifecycle state of a component (like an activity or a fragment) and allows other objects to observe this state.

Lifecycle是一个类，它包含一个组件的生命周期状态的信息(比如一个活动或一个片段)，并允许其他对象观察这个状态。

[`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) uses two main enumerations to track the lifecycle status for its associated component:

生命周期使用两个主要的枚举来跟踪其关联组件的生命周期状态:

- Event 事件

  The lifecycle events that are dispatched from the framework and the [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) class. These events map to the callback events in activities and fragments.

  从框架和生命周期类中发送的生命周期事件。这些事件映射到活动和片段中的回调事件。

- State 状态

  The current state of the component tracked by the [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) object.

  生命周期对象跟踪的组件的当前状态。

![1](https://developer.android.com/images/topic/libraries/architecture/lifecycle-states.png)

Think of the states as nodes of a graph and events as the edges between these nodes.

将状态视为图的节点，将事件视为这些节点之间的边。

A class can monitor the component's lifecycle status by adding annotations to its methods. Then you can add an observer by calling the[`addObserver()`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html#addObserver(android.arch.lifecycle.LifecycleObserver)) method of the [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) class and passing an instance of your observer, as shown in the following example:

类可以通过向其方法添加注释来监视组件的生命周期状态。然后，可以通过调用生命周期类的addobserver()方法添加一个观察者，并传递一个观察者的实例，如下例所示:

```
public class MyObserver implements LifecycleObserver {
    @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
    public void connectListener() {
        ...
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
    public void disconnectListener() {
        ...
    }
}

myLifecycleOwner.getLifecycle().addObserver(new MyObserver());
```

In the example above, the `myLifecycleOwner` object implements the [`LifecycleOwner`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html) interface, which is explained in the following section.

在上面的例子中，myLifecycleOwner对象实现了LifecycleOwner接口，在下面的部分中对此进行了解释。

## LifecycleOwner

------

[`LifecycleOwner`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html) is a single method interface that denotes that the class has a [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html). It has one method, [`getLifecycle()`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html#getLifecycle()), which must be implemented by the class. If you're trying to manage the lifecycle of a whole application process instead, see [`ProcessLifecycleOwner`](https://developer.android.com/reference/android/arch/lifecycle/ProcessLifecycleOwner.html).

LifecycleOwner是一个单一的方法接口，它表示类具有生命周期。它有一个方法getLifecycle()，它必须由类来实现。如果您试图管理整个应用程序流程的生命周期，请参见ProcessLifecycleOwner。

This interface abstracts the ownership of a [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) from individual classes, such as `Fragment` and `AppCompatActivity`, and allows writing components that work with them. Any custom application class can implement the [`LifecycleOwner`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html) interface.

该接口抽象了单个类(例如片段和AppCompatActivity)的生命周期的所有权，并允许编写与它们一起工作的组件。任何自定义应用程序类都可以实现LifecycleOwner接口。

Components that implement [`LifecycleObserver`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleObserver.html) work seamlessly with components that implement [`LifecycleOwner`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html) because an owner can provide a lifecycle, which an observer can register to watch.

实现生命周期的组件与实现生命周期的组件无缝地工作，因为所有者可以提供一个生命周期，一个观察者可以注册观察。

For the location tracking example, we can make the `MyLocationListener` class implement [`LifecycleObserver`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleObserver.html) and then initialize it with the activity's[`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) in the `onCreate()` method. This allows the `MyLocationListener` class to be self-sufficient, meaning that the logic to react to changes in lifecycle status is declared in `MyLocationListener` instead of the activity. Having the individual components store their own logic makes the activities and fragments logic easier to manage.

对于位置跟踪示例，我们可以使MyLocationListener类实现LifecycleObserver，然后在onCreate()方法中使用活动的slifecycle初始化它。这允许MyLocationListener类实现自给自足，这意味着在MyLocationListener中声明了对生命周期状态变化做出反应的逻辑，而不是在活动中声明。让单个组件存储它们自己的逻辑使活动和片段逻辑更易于管理。

```
class MyActivity extends AppCompatActivity {
    private MyLocationListener myLocationListener;

    public void onCreate(...) {
        myLocationListener = new MyLocationListener(this, getLifecycle(), location -> {
            // update UI
        });
        Util.checkUserStatus(result -> {
            if (result) {
                myLocationListener.enable();
            }
        });
  }
}
```

A common use case is to avoid invoking certain callbacks if the [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) isn't in a good state right now. For example, if the callback runs a fragment transaction after the activity state is saved, it would trigger a crash, so we would never want to invoke that callback.

一个常见的用例是，如果生命周期不处于良好状态，则避免调用某些回调。例如，如果回调在活动状态保存后运行一个片段事务，它将触发一个崩溃，因此我们永远不会调用那个回调。

To make this use case easy, the [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) class allows other objects to query the current state.

为了简化这个用例，生命周期类允许其他对象查询当前状态。

```
class MyLocationListener implements LifecycleObserver {
    private boolean enabled = false;
    public MyLocationListener(Context context, Lifecycle lifecycle, Callback callback) {
       ...
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_START)
    void start() {
        if (enabled) {
           // connect
        }
    }

    public void enable() {
        enabled = true;
        if (lifecycle.getCurrentState().isAtLeast(STARTED)) {
            // connect if not connected
        }
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
    void stop() {
        // disconnect if connected
    }
}
```

With this implementation, our `LocationListener` class is completely lifecycle-aware. If we need to use our `LocationListener` from another activity or fragment, we just need to initialize it. All of the setup and teardown operations are managed by the class itself.

通过这个实现，我们的LocationListener类是完全生命周期感知的。如果我们需要从另一个活动或片段中使用LocationListener，我们只需要初始化它。所有的setup和teardown操作都由类本身管理。

If a library provides classes that need to work with the Android lifecycle, we recommend that you use lifecycle-aware components. Your library clients can easily integrate those components without manual lifecycle management on the client side.

如果一个库提供了需要使用Android生命周期的类，那么我们建议您使用生命周期感知组件。您的库客户端可以轻松地集成这些组件，而无需在客户端使用手动的生命周期管理。

### Implementing a custom LifecycleOwner

###实现一个自定义LifecycleOwner

Fragments and Activities in Support Library 26.1.0 and later already implement the [`LifecycleOwner`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html) interface.支持库26.1.0的片段和活动已经实现了LifecycleOwner接口。



If you have a custom class that you would like to make a [`LifecycleOwner`](https://developer.android.com/reference/android/arch/lifecycle/LifecycleOwner.html), you can use the [LifecycleRegistry](https://developer.android.com/reference/android/arch/lifecycle/LifecycleRegistry.html) class, but you need to forward events into that class, as shown in the following code example:

如果您有一个定制的类，您想要创建一个生命周期的所有者，您可以使用LifecycleRegistry类，但是您需要将事件转发到该类，如下面的代码示例所示:

```
public class MyActivity extends Activity implements LifecycleOwner {
    private LifecycleRegistry mLifecycleRegistry;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mLifecycleRegistry = new LifecycleRegistry(this);
        mLifecycleRegistry.markState(Lifecycle.State.CREATED);
    }

    @Override
    public void onStart() {
        super.onStart();
        mLifecycleRegistry.markState(Lifecycle.State.STARTED);
    }

    @NonNull
    @Override
    public Lifecycle getLifecycle() {
        return mLifecycleRegistry;
    }
}
```

## Best practices for lifecycle-aware components



------

- Keep your UI controllers (activities and fragments) as lean as possible. They should not try to acquire their own data; instead, use a [`ViewModel`](https://developer.android.com/reference/android/arch/lifecycle/ViewModel.html) to do that, and observe a [`LiveData`](https://developer.android.com/reference/android/arch/lifecycle/LiveData.html) object to reflect the changes back to the views.
- Try to write data-driven UIs where your UI controller’s responsibility is to update the views as data changes, or notify user actions back to the[`ViewModel`](https://developer.android.com/reference/android/arch/lifecycle/ViewModel.html).
- Put your data logic in your [`ViewModel`](https://developer.android.com/reference/android/arch/lifecycle/ViewModel.html) class. [`ViewModel`](https://developer.android.com/reference/android/arch/lifecycle/ViewModel.html) should serve as the connector between your UI controller and the rest of your app. Be careful though, it isn't [`ViewModel`](https://developer.android.com/reference/android/arch/lifecycle/ViewModel.html)'s responsibility to fetch data (for example, from a network). Instead, [`ViewModel`](https://developer.android.com/reference/android/arch/lifecycle/ViewModel.html) should call the appropriate component to fetch the data, then provide the result back to the UI controller.
- Use [Data Binding](https://developer.android.com/topic/libraries/data-binding/index.html) to maintain a clean interface between your views and the UI controller. This allows you to make your views more declarative and minimize the update code you need to write in your activities and fragments. If you prefer to do this in the Java programming language, use a library like [Butter Knife](http://jakewharton.github.io/butterknife/) to avoid boilerplate code and have a better abstraction.
- If your UI is complex, consider creating a [presenter](http://www.gwtproject.org/articles/mvp-architecture.html#presenter) class to handle UI modifications. This might be a laborious task, but it can make your UI components easier to test.
- Avoid referencing a `View` or `Activity` context in your [`ViewModel`](https://developer.android.com/reference/android/arch/lifecycle/ViewModel.html). If the `ViewModel` outlives the activity (in case of configuration changes), your activity leaks and isn't properly disposed by the garbage collector.

###生命周期感知组件的最佳实践

- 保持你的UI控制器(活动和片段)尽可能精简。他们不应该试图获取他们自己的数据;相反，使用ViewModel来实现这一点，并观察一个LiveData对象，以反映对视图的更改。
- 尝试编写数据驱动的UI, UI控制器的职责是在数据更改时更新视图，或者将用户操作通知到viewmodel。
- 将数据逻辑放在ViewModel类中。ViewModel应该作为UI控制器和其他应用程序之间的连接器。但是要小心，它不是ViewModel的职责来获取数据(例如，从网络中获取数据)。相反，ViewModel应该调用适当的组件来获取数据，然后将结果返回给UI控制器。
- 使用数据绑定来保持视图和UI控制器之间的整洁界面。这使您可以使您的视图更具声明性，并且最小化您在活动和片段中需要编写的更新代码。如果您喜欢在Java编程语言中这样做，可以使用像黄油刀这样的库来避免样板代码，并有更好的抽象。
- 如果你的UI很复杂，考虑创建一个演示类来处理UI修改。这可能是一项艰苦的任务，但它可以使您的UI组件更容易测试。
- 避免在ViewModel中引用视图或活动上下文。如果ViewModel超过了活动(在配置更改的情况下)，那么您的活动就会泄漏，而垃圾收集器不会正确地处理它。

## Use cases for lifecycle-aware components

用于生命周期感知组件的用例。

------

Lifecycle-aware components can make it much easier for you to manage lifecycles in a variety of cases. A few examples are:

生命周期感知组件可以使您在各种情况下更容易地管理生命周期。几个例子:

- Switching between coarse and fine-grained location updates. Use lifecycle-aware components to enable fine-grained location updates while your location app is visible and switch to coarse-grained updates when the app is in the background. [`LiveData`](https://developer.android.com/reference/android/arch/lifecycle/LiveData.html), a lifecycle-aware component, allows your app to automatically update the UI when your use changes locations.
- 在粗粒度和细粒度的位置更新之间切换。使用生命周期感知组件来启用细粒度的位置更新，而当应用程序在后台时，您的位置应用程序是可见的，并切换到粗粒度的更新。LiveData，一个生命周期感知组件，允许你的应用程序自动更新用户界面，当你的使用改变位置。
- Stopping and starting video buffering. Use lifecycle-aware components to start video buffering as soon as possible, but defer playback until app is fully started. You can also use lifecycle-aware components to terminate buffering when your app is destroyed.
- 停止和启动视频缓冲。使用生命周期感知组件尽快启动视频缓冲，但延迟播放直到应用程序完全启动。当应用程序被破坏时，您还可以使用生命周期感知组件来终止缓冲。
- Starting and stopping network connectivity. Use lifecycle-aware components to enable live updating (streaming) of network data while an app is in the foreground and also to automatically pause when the app goes into the background.
- 启动和停止网络连接。使用生命周期感知组件来激活网络数据的实时更新(流)，而应用程序在前台，当应用程序进入后台时也会自动暂停。
- Pausing and resuming animated drawables. Use lifecycle-aware components to handle pausing animated drawables when while app is in the background and resume drawables after the app is in the foreground.
- -暂停和恢复动画绘制。当应用程序在后台运行时，使用生命周期感知组件来处理暂停的动画绘制，应用程序在前台后恢复可绘制的内容。

## Handling on stop events 在停止事件处理

------

When a [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) belongs to an `AppCompatActivity` or `Fragment`, the [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html)'s state changes to [`CREATED`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.State.html#CREATED) and the [`ON_STOP`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.Event.html#ON_STOP) event is dispatched when the `AppCompatActivity` or `Fragment`'s `onSaveInstanceState()` is called.当一个生命周期属于一个AppCompatActivity或Fragment时，生命周期的状态会发生变化，当AppCompatActivity或Fragment的onSaveInstanceState()被调用时，ON_STOP事件就会被发送。







为了防止这个问题，在版本beta2和更低的状态下的生命周期类会在不发送事件的情况下创建状态，这样任何检查当前状态的代码都会得到真正的值，即使在系统调用onStop()之前，事件不会被发送。

When a `Fragment` or `AppCompatActivity`'s state is saved via `onSaveInstanceState()`, it's UI is considered immutable until [`ON_START`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.Event.html#ON_START) is called. Trying to modify the UI after the state is saved is likely to cause inconsistencies in the navigation state of your application which is why `FragmentManager` throws an exception if the app runs a `FragmentTransaction` after state is saved. See `commit()` for details

.当一个片段或AppCompatActivity的状态通过onSaveInstanceState()保存时，它的UI被认为是不可变的，直到ON_START被调用。在保存状态后尝试修改UI可能会导致应用程序的导航状态不一致，这就是为什么在保存了状态后，FragmentManager会抛出一个异常。有关详细信息,请参阅commit()。

[`LiveData`](https://developer.android.com/reference/android/arch/lifecycle/LiveData.html) prevents this edge case out of the box by refraining from calling its observer if the observer's associated [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) isn't at least [`STARTED`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.State.html#STARTED). Behind the scenes, it calls [`isAtLeast()`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.State.html#isAtLeast(android.arch.lifecycle.Lifecycle.State)) before deciding to invoke its observer.

如果观察者的相关生命周期不启动，LiveData就可以避免通过调用它的观察者来避免这种边缘情况。在幕后，它调用isAtLeast()，然后决定调用它的观察者。

Unfortunately, `AppCompatActivity`'s `onStop()` method is called *after* `onSaveInstanceState()`, which leaves a gap where UI state changes are not allowed but the [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) has not yet been moved to the [`CREATED`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.State.html#CREATED) state.

不幸的是，AppCompatActivity的onStop()方法是在onSaveInstanceState()之后调用的，它留下了一个不允许UI状态更改的空白，但是生命周期还没有被移动到创建的状态。

To prevent this issue, the [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) class in version `beta2` and lower mark the state as [`CREATED`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.State.html#CREATED) without dispatching the event so that any code that checks the current state gets the real value even though the event isn't dispatched until `onStop()` is called by the system.

为了防止这个问题，在版本beta2和更低的状态下的生命周期类会在不发送事件的情况下创建状态，这样任何检查当前状态的代码都会得到真正的值，即使在系统调用onStop()之前，事件不会被发送。

Unfortunately, this solution has two major problems:

不幸的是，这个解决方案有两个主要问题:

- On API level 23 and lower, the Android system actually saves the state of an activity even if it is *partially* covered by another activity. In other words, the Android system calls `onSaveInstanceState()` but it doesn't necessarily call `onStop()`. This creates a potentially long interval where the observer still thinks that the lifecycle is active even though its UI state can't be modified.

- 在API级别23和更低的情况下，Android系统实际上保存了一个活动的状态，即使它被另一个活动部分覆盖。换句话说，Android系统调用onSaveInstanceState()，但它并不一定调用onStop()。这将创建一个潜在的长时间间隔，观察者仍然认为生命周期是活动的，即使它的UI状态不能被修改。
  ​

- Any class that wants to expose a similar behavior to the [`LiveData`](https://developer.android.com/reference/android/arch/lifecycle/LiveData.html) class has to implement the workaround provided by [`Lifecycle`](https://developer.android.com/reference/android/arch/lifecycle/Lifecycle.html) version `beta 2`and lower.

- 任何想要向LiveData类公开类似行为的类都必须实现生命周期版本beta 2和更低版本提供的解决方案。

  ​

```
Note: To make this flow simpler and provide better compatibility with older versions, starting at version 1.0.0-rc1, Lifecycle objects are marked as CREATED and ON_STOP is dispatched when onSaveInstanceState() is called without waiting for a call to the onStop() method. This is unlikely to impact your code but it is something you need to be aware of as it doesn't match the call order in the Activity class in API level 26 and lower.
```

```
注为了使这个流程更简单，并提供与旧版本更好的兼容性，从版本1.0 -rc1开始，生命周期对象被标记为创建，ON_STOP在调用onSaveInstanceState()时被调用，而无需等待onStop()方法的调用。这不大可能影响您的代码，但是您需要注意的是，它与API级别26和以下的Activity类中的调用顺序不匹配。
```



