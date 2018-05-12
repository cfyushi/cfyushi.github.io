## 官方说明

android:launchMode

有关应如何启动 Activity 的指令。共有四种模式与 `Intent` 对象中的 Activity 标志（`FLAG_ACTIVITY_*` 常量）协同工作，以确定在调用 Activity 处理` Intent` 时应执行的操作。 这些模式是`“standard”`、`“singleTop”`、`“singleTask”`、`“singleInstance”`，默认模式`“standard”`。

| 用例                             | 启动模式           | 多个实例？ | 注释                                                         |
| -------------------------------- | ------------------ | ---------- | ------------------------------------------------------------ |
| 大多数 Activity 的正常启动       | `“standard”`       | 是         | 默认值。系统始终会在目标任务中创建新的 Activity 实例并向其传送 Intent |
| 大多数 Activity 的正常启动       | `“singleTop”`      | 有条件     | 如果目标任务的顶部已存在一个 Activity 实例，则系统会通过调用该实例的 `onNewIntent()` 方法向其传送 Intent，而不是创建新的 Activity 实例 |
| 专用启动*（不建议用作常规用途）* | `“singleTask”`     | 否         | 系统在新任务的根位置创建 Activity 并向其传送 Intent。 不过，如果已存在一个 Activity 实例，则系统会通过调用该实例的 `onNewIntent()` 方法向其传送 Intent，而不是创建新的 Activity 实例 |
| 专用启动*（不建议用作常规用途）* | `“singleInstance”` | 否         | 与“`singleTask"`”相同，只是系统不会将任何其他 Activity 启动到包含实例的任务中。 该 Activity 始终是其任务唯一仅有的成员 |



如上表所示，这些模式分为两大类，`“standard"`和`"singleTop“`Activity 为一类，`"singleTask"`和`"singleInstance"`为另一类

**使用“`standard`”或“`singleTop`”启动模式的 Activity 可多次实例化。 实例可归属任何任务，并且可以位于 Activity 堆栈中的任何位置**。 它们通常启动到名为 `startActivity()` 的任务之中（除非 Intent 对象包含 `FLAG_ACTIVITY_NEW_TASK` 指令，在此情况下会选择其他任务 — 请参阅 [taskAffinity](https://developer.android.com/guide/topics/manifest/activity-element#aff) 属性）。

相比之下，**“`singleTask`”和“`singleInstance`”Activity 只能启动任务。 它们始终位于 Activity 堆栈的根位置。此外，设备一次只能保留一个 Activity 实例 — 只允许一个此类任务**。

“`standard`”和“`singleTop`”模式只在一个方面有差异： 每次“`standard`”Activity 有新的 Intent 时，系统都会创建新的类实例来响应该 Intent。每个实例处理单个 Intent。同理，也可创建新的“`singleTop`”Activity 实例来处理新的 Intent。 不过，如果目标任务在其堆栈顶部已有一个 Activity 实例，那么该实例将接收新 Intent（通过调用 `onNewIntent()`）；此时不会创建新实例。在其他情况下 — 例如，如果“`singleTop`”的一个现有实例虽在目标任务内，但未处于堆栈顶部，或者虽然位于堆栈顶部，但不在目标任务中 — 则系统会创建一个新实例并将其推送到堆栈上。

同理，如果您[向上导航](https://developer.android.com/training/implementing-navigation/ancestral.html)到当前堆栈上的某个 Activity，该行为由父 Activity 的启动模式决定。 如果父 Activity 有启动模式 `singleTop`（或 `up` Intent 包含 `FLAG_ACTIVITY_CLEAR_TOP`），则系统会将该父项置于堆栈顶部，并保留其状态。 导航 Intent 由父 Activity 的 `onNewIntent()` 方法接收。 如果父 Activity 有启动模式 `standard`（并且 `up` Intent 不包含 `FLAG_ACTIVITY_CLEAR_TOP`），则系统会将当前 Activity 及其父项同时弹出堆栈，并创建一个新的父 Activity 实例来接收导航 Intent。

“`singleTask`”和“`singleInstance`”模式同样只在一个方面有差异： **“`singleTask`”Activity 允许其他 Activity 成为其任务的组成部分。 它始终位于其任务的根位置，但其他 Activity（必然是“`standard`”和“`singleTop`”Activity）可以启动到该任务中**。 相反，**“`singleInstance`”Activity 则不允许其他 Activity 成为其任务的组成部分。它是任务中唯一的 Activity。 如果它启动另一个 Activity，系统会将该 Activity 分配给其他任务** — 就好像 Intent 中包含 `FLAG_ACTIVITY_NEW_TASK` 一样。

