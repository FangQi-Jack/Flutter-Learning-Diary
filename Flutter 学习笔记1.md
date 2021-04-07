# Flutter 学习笔记 1

## Flutter 是怎么运转的?
Flutter 重写了一整套包括底层渲染逻辑和上层开发语言的完整解决方案。

### Flutter 与 React Native 的本质区别
>  RN 通过 JavaScript 虚拟机扩展调用系统组件，然后由原生系统进行组件的渲染。
>  Flutter 则是自己完成组件渲染。

## Flutter 是怎么完成组件渲染的?
![Flutter绘制原理](https://static001.geekbang.org/resource/image/95/2a/95cb258c9103e05398f9c97a1113072a.png)

## Skia 是什么？
Skia 是一款用 C++ 开发的、性能彪悍的 2D 图像绘制引擎。

## Flutter 的原理
****![Flutter 的架构](https://static001.geekbang.org/resource/image/ac/2f/ac7d1cec200f7ea7cb6cbab04eda252f.png)
Flutter 架构采用分层设计，从下到上分为三层，依次为：Embedder、Engine、Framework。
* Embedder 是操作系统适配层，实现了渲染 Surface 设置，线程设置，以及平台插件等平台相关特性的适配。从这里我们可以看到，Flutter 平台相关特性并不多，这就使得从框架层面保持跨端一致性的成本相对较低。
* Engine 层主要包含 Skia、Dart 和 Text，实现了 Flutter 的渲染引擎、文字排版、事件处理和 Dart 运行时等功能。Skia 和 Text 为上层接口提供了调用底层渲染和排版的能力，Dart 则为 Flutter 提供了运行时调用 Dart 和渲染引擎的能力。而 Engine 层的作用，则是将它们组合起来，从它们生成的数据中实现视图渲染。
* Framework 层则是一个用 Dart 实现的 UI SDK，包含了动画、图形绘制和手势识别等功能。为了在绘制控件等固定样式的图形时提供更直观、更方便的接口，Flutter 还基于这些基础能力，根据 Material 和 Cupertino 两种视觉设计风格封装了一套 UI 组件库。我们在开发 Flutter 的时候，可以直接使用这些组件库。

## Flutter 界面渲染过程
Flutter 的界面渲染过程包括：布局、绘制、合并、渲染。
### 布局
Flutter 采用深度优先机制遍历渲染对象树，决定渲染对象树中各渲染对象在屏幕上的位置和尺寸。在布局过程中，渲染对象树中的每个渲染对象都会接收父对象的布局约束参数，决定自己的大小，然后父对象按照控件逻辑决定各个子对象的位置，完成布局过程。
![Flutter 的布局过程](https://static001.geekbang.org/resource/image/f9/00/f9e6bbf06231fbad54ed11ef291e8d00.png)
为了防止因子节点发生变化而导致整个控件树重新布局，Flutter 加入了一个机制—**`布局边界（Relayout Boundary）`**，可以在某些节点自动或手动地设置布局边界，当边界内的任何对象发生重新布局时，不会影响边界外的对象，反之亦然。
![Flutter 布局边界](https://static001.geekbang.org/resource/image/42/de/42961a84ecc8181d1afe7ffbaa1a55de.png)
### 绘制
与布局过程一样，绘制过程也是深度优先遍历，而且总是先绘制自身，再绘制子节点。
![Flutter 的绘制](https://static001.geekbang.org/resource/image/8c/b8/8c1d612990d9ada0508c5a41c9e4cab8.png)
可以看到，由于一些其他原因（比如，视图手动合并）导致 2 的子节点 5 与它的兄弟节点 6 处于了同一层，这样会导致当节点 2 需要重绘的时候，与其无关的节点 6 也会被重绘，带来性能损耗。
为了解决这一问题，Flutter 提出了与布局边界对应的机制——**`重绘边界（Repaint Boundary）`**。在重绘边界内，Flutter 会强制切换新的图层，这样就可以避免边界内外的互相影响，避免无关内容置于同一图层引起不必要的重绘。
![重绘边界](https://static001.geekbang.org/resource/image/da/ee/da430de8f265f444c4801758902a8bee.png)
### 合并和渲染
Flutter 的渲染树通常都比较复杂，如果直接交由渲染引擎进行多图层渲染，可能会出现大量渲染内容的重复绘制，因此还需要进行一次图层合成，即将所有的图层根据大小、层级、透明度等规则计算出最终的显示效果，将相同的图层归类合并，简化渲染树，提高渲染效率。
合并完成后，Flutter 会将几何图层数据交由 Skia 引擎加工成二维图像数据，最终交由 GPU 进行渲染，完成界面的展示。

## Flutter 知识体系
![Flutter 知识体系](https://static001.geekbang.org/resource/image/99/64/9959006fe52706a123cc7fc596346064.jpg)
