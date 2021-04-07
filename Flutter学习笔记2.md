# Flutter 学习笔记2
**Flutter 的核心设计思想便是“一切皆 Widget”**
Flutter 将视图数据的组织和渲染抽象为三个部分：Element、Widget、RenderObject。
![Widget、Element、RenderObject](https://static001.geekbang.org/resource/image/b4/c9/b4ae98fe5b4c9a7a784c916fd140bbc9.png)

## Widget
Flutter 中的 Widget 是不可变的，因此当视图数据发生变化时，Flutter 会将整个视图树销毁重建。不过 Widget 并不涉及实际渲染位图，所以它只是一份轻量级的数据结构，因此重建的成本不高。而且由于 Widget 是不可变的，可以以较低的成本进行渲染节点复用，所以一个实际的渲染树中可能存在多个不同 Widget 对应同一个渲染节点的情况，这也同样降低了 Widget 重建的成本。

## Element
Element 是 Widget 的实例化对象，它承载了视图构建的上下文数据，是连接结构化的配置信息到完成最终渲染的桥梁。
Flutter 的渲染过程分为三步：
* 通过 Widget 树生成对应的 Element 树
* 创建相应的 RenderObject 并关联到 Element.renderObject 属性上
* 构建 RenderObject 树完成最终的渲染
因为 Widget 具有不可变性，但 Element 却是可变的。实际上，Element 树这一层将 Widget 树的变化做了抽象，可以只将真正需要修改的部分同步到真实的 RenderObject 树中，最大程度降低对真实渲染视图的修改，提高渲染效率，而不是销毁整个渲染视图树重建。

## RenderObject
渲染对象树在 Flutter 的展示过程分为四个阶段，即布局、绘制、合成和渲染。 其中，布局和绘制在 RenderObject 中完成，Flutter 采用深度优先机制遍历渲染对象树，确定树中各个对象的位置和尺寸，并把它们绘制到不同的图层上。绘制完毕后，合成和渲染的工作则交给 Skia 搞定。

## RenderObjectWidget
在 Flutter 中，布局和绘制工作实际是在 RenderObjectWidget 中完成的。
RenderObjectWidget 是一个抽象类，拥有 创建 Element、创建 RenderObject 以及更新 RenderObject 的方法。
**但是实际上 RenderObject 并不负责这些对象的创建和更新。**
对于 Element 的创建，会在遍历 Widget 树时调用 createElement 方法去同步 Widget 自身配置，从而生成对应节点的 Element 对象。而对于 RenderObject 的创建与更新则实际在 RenderObjectElement 类中完成。
Element 创建完成后， Flutter 会调用 Element 的 mount 方法，在该方法中会完成与之关联的 RenderObject 的创建以及渲染树的插入工作，插入到渲染树中的 Element 就可以显示到屏幕上了。
如果 Widget 的配置数据发生了改变，那么持有该 Widget 的 Element 节点也会被标记为 dirty。在下一个周期的绘制时，Flutter 就会触发 Element 树的更新，并使用最新的 Widget 数据更新自身以及关联的 RenderObject 对象，接下来便会进入 Layout 和 Paint 的流程。而真正的绘制和布局过程，则完全交由 RenderObject 完成。
