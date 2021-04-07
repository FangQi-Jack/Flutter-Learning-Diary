# Widget 中的 State 是什么？
Widget 有两种：**StatelessWidget** 和 **StatefulWidget**。StatelessWidget 用于静态的、无状态变化的场景，而 StatefulWidget 则用于动态的、有状态变化的视图展示。
**当需要构建的界面不随任何状态信息的变化而变化时，使用 StatelessWidget，反之则使用 StatefulWidget。**

## StatelessWidget
![StatelessWidget](https://static001.geekbang.org/resource/image/3e/cc/3ec97a9f584132c2bcdbca60fd2888cc.png)
#### 什么场景下应该使用 StatelessWidget 呢？
**父 Widget 是否能通过初始化参数完全控制其 UI 展示效果？**如果能，那么我们就可以使用 StatelessWidget 来设计构造函数接口了。

## StatefulWidget
![StatefulWiget](https://static001.geekbang.org/resource/image/8a/f6/8ae7bf36f618a999da8847cbb4da4bf6.png)
StatefulWidget 自身并没有实现试图构建，而是通过 State 类代理来完成真正的视图构建。 

## 不要滥用 StatefulWidget
StatelessWidget 能够实现的通过 StatefulWidget 也都能实现，但是， StatefulWidget 并不是 “万能”的。由于 Flutter 中 Widget 是不可变的，当 UI 发生变化时，Flutter 会通过销毁重建视图树的方式完成 UI 的更新，所以如果对于一些静态的 UI 仍然使用 StatefulWidget 来实现的话会增加 Flutter UI 刷新的成本。比如如果将一个页面的根节点用 StatefulWidget 来实现，那么其每一次更新 UI 都将销毁所有节点然后重建，这很明显增加了性能损耗。
**正确评估你的视图展示需求，避免无谓的 StatefulWidget 使用，是提高 Flutter 应用渲染性能最简单也是最直接的手段。**