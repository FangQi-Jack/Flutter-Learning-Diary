# 生命周期
State 承载了 StatefulWidget 的生命周期。

## State 的生命周期
指在用户参与交互的情况下，与其关联的 Widget 从创建到显示再到更新最后到停止，直至销毁的各个阶段。
![State 的生命周期](https://static001.geekbang.org/resource/image/bb/84/bba88ebb44b7fdd6735f3ddb41106784.png)
State 的生命周期基本分为三个阶段：创建、更新、销毁。

### 创建
State 在初始化时会依次调用：构造方法 -> initState -> didChangeDependencies -> build，完成视图渲染。
* 构造方法是 State 生命周期的起点，Flutter 通过调用 StatefulWidget.createState() 来创建一个 State 对象。在构造方法中接收父 Widget 传递的配置信息，这些决定了 Widget 的初始展示样式。
* initState 会在 State 插入视图树时被调用，该方法在 State 的生命周期中只会被调用一次。
* didChangeDependencies 用来处理 State 对象依赖关系变化，会在 initState 之后被调用。
* build 用于构建视图，经过以上步骤，Framework 认为 State 已经准备好了，于是调用 build 完成视图构建。

### 更新
Widget 的更新主要由三个方法触发：setState、didChangeDependencies、didUpdateWidget。
* setState：当数据发生变化时，我们总是通过调用此方法通知 Flutter 更新 Widget。
* didChangeDependencies：State 对象的依赖关系发生变化后，Flutter 会回调此方法触发组件构建。比如系统语言发生变化。
* didUpdateWidget：当 Widget 配置发生变化时被调用。比如父 Widget 触发重建。

### 销毁
当组件被移除或页面被销毁时 Flutter 会调用 deactivate 和 dispose 两个方法完成组件的移除或销毁。
* 当组件的可见状态发生变化时会调用 deactivate ，此时 State 被暂时的从视图树中移除。页面切换时，由于组件在视图树中的位置发生了变化，需要先暂时移除后再重新添加，重新触发组件构建，因此该方法也会被调用。
* 当组件被永久的从视图树中移除时，Flutter 会调用 dispose 方法。

![](https://static001.geekbang.org/resource/image/aa/bc/aacfcfdb80038874251aa8ad93930abc.png)

## 生命周期监听
我们可以通过 **WidgetsBindingObserver** 来实现对一些生命周期的监听。
