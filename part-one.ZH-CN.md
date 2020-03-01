# Part-I

这是本教程的第一部分。这节教程的重点是你将学习到 Fiber 的相关结构和字段的描述，以及为你的渲染器 (renderer) 配置 host configuration 并注入到 devtools 中。

本节，我们将使用 [`react-reconciler`](https://github.com/facebook/react/tree/master/packages/react-reconciler) 来构建一个 React reconciler 。以及我们将用 Fiber 来实现渲染器 (renderer) 。早前， React 是在传统的 JavaScript stack 之上来实现 **stack renderer** 渲染的。
另一方面，Fiber 受 algebraic effects 和 functional ideas 的影响。 它可以被认为是包含有关的成分，它的输入，并且其输出的信息的 JavaScript 对象。

在我们继续之前，我需要推荐你先阅读这篇由 [Andrew Clark](https://twitter.com/acdlite?lang=en) 写的关于 Fiber 架构的[文章](https://github.com/acdlite/react-fiber-architecture)。它有助于你更好的理解。

让我们一起上吧！

那么我们需要先安装以下依赖。

```
npm install react-reconciler fbjs
```

然后我们需要从 `react-reconciler` 导入 `Reconciler` 以及其他模块。

```js
import Reconciler from 'react-reconciler';
import emptyObject from 'fbjs/lib/emptyObject';

import { createElement } from './utils/createElement';
```

注意我们也需要导入 `createElement` 函数。稍后，我们会实现这个函数。

接着，我们将使用 `Reconciler` 来创建一个接收 **host config** 对象的 reconciler 实例。在这个对象中，我们需要定义一些关于渲染的生命周期的方法 (update, append children, remove children, commit)。React 则管理所有非宿主组件 (non-host components)，无状态组件(stateless)和复合组件(composites)。

```js
const WordRenderer = Reconciler({
  appendInitialChild(parentInstance, child) {
    if (parentInstance.appendChild) {
      parentInstance.appendChild(child);
    } else {
      parentInstance.document = child;
    }
  },

  createInstance(type, props, internalInstanceHandle) {
    return createElement(type, props, internalInstanceHandle);
  },

  createTextInstance(text, rootContainerInstance, internalInstanceHandle) {
    return text;
  },

  finalizeInitialChildren(wordElement, type, props) {
    return false;
  },

  getPublicInstance(inst) {
    return inst;
  },

  prepareForCommit() {
    // noop
  },

  prepareUpdate(wordElement, type, oldProps, newProps) {
    return true;
  },

  resetAfterCommit() {
    // noop
  },

  resetTextContent(wordElement) {
    // noop
  },

  getRootHostContext(rootInstance) {
    // You can use this 'rootInstance' to pass data from the roots.
  },

  getChildHostContext() {
    return emptyObject;
  },

  shouldSetTextContent(type, props) {
    return false;
  },

  now: () => {}

  supportsMutation: false
});
```

接着，让我们分解一下我们的 host config -

**`createInstance`**

该方法需要通过 `type`， `props` 和 `internalInstanceHandle` 参数来创建一个组件实例。

Example - Let's say we render,

下面，我们所说的需要渲染的例子，

```js
<Text>Hello World</Text>
```

`createInstance` 将返回关于这个元素 (' TEXT ') 的 `type` ，属性 ( { children: 'Hello World' } ) 以及它的 root 实例 (`WordDocument`) 。

**Fiber**

A fiber is work on a component that needs to be done or was done. Atmost, a component instance has two fibers, flushed fiber and work in progress fiber.

`internalInstanceHandle` contains information about the `tag`, `type`, `key`, `stateNode`, and the return fiber. This object (fiber) further contains information about -

- **`tag`** - fiber 的类型
- **`key`** - 子组件的唯一识别码
- **`type`** - 与 fiber 相关的 function/class/module
- **`stateNode`** - 与 fiber 相关的状态

- **`return`** - The fiber to return to after finishing processing this one (parent fiber).
- **`child`** - `child`, `sibling` and `index` represents the **singly linked list data structure**.
- **`sibling`**
- **`index`**
- **`ref`** - The ref last used to attach this (parent) node.

- **`pendingProps`** - This property is useful when a tag is overloaded.
- **`memoizedProps`** - The props used to create the output.
- **`updateQueue`** - A queue of state updates and callbacks.
- **`memoizedState`** - The state used to create the output.

- **`internalContextTag`** - Bit field data structure. React Fiber uses bit field data structures to hold a sequence of information about the fiber and it's subtree which is stored in an adjacent computer memory locations. A bit within this set is used to determine the state of an attribute. Collection of bit fields called flags represent the outcome of an operation or some intermediate state. React Fiber uses AsyncUpdates flag which indicates whether the subtree is async is or not.
- **`effectTag`** - Effect
- **`nextEffect`** - Singly linked list fast path to the next fiber with side-effects.
- **`firstEffect`** - The first(firstEffect) and last(lastEffect) fiber with side-effect within the subtree. Reuse the work done in this fiber.

- **`expirationTime`** - This represents a time in the future by which the work should be completed.
- **`alternate`** - Pooled version of fiber which contains information about the fiber and is ready to be used rather than allocated on use. In computer graphics, this concept is abstracted in **double buffer** pattern. It uses more memory but we can clean up the pairs.

- `pendingWorkPriority`
- `progressedPriority`
- `progressedChild`
- `progressedFirstDeletion`
- `progressedLastDeletion`

**`appendInitialChild`**

It appends the children. If children are wrapped inside a parent component (eg: `Document`), then we will add all the children to it else we
will create a property called `document` on a parent node and append all the children to it. This will be helpful when we will parse the input component
and make a call to the render method of our component.

例如 -

```js
const data = document.render(); // returns the output
```

**`prepareUpdate`**

It computes the diff for an instance. Fiber can reuse this work even if it pauses or abort rendering a part of the tree.
该方法用来对应实例的差异。Fiber 。

**`commitUpdate`**

提交更新或者将计算的差异应用到宿主环境 (WordDocument) 的节点 (node) 中

**`commitMount`**

Renderer mounts the host component but may schedule some work to done after like auto-focus on forms. The host components are only mounted when there is no current/alternate fiber.

**`hostContext`**

Host context is an internal object which our renderer may use based on the current location in the tree. In DOM, this object
is required to make correct calls for example to create an element in html or in MathMl based on current context.

**`getPublicInstance`**

This is an identity relation which means that it always returns the same value that was used as its argument. It was added for the TestRenderers.

**`resetTextContent`**
Reset the text content of the parent before doing any insertions (inserting host nodes into the parent). This is similar to double buffering technique in OpenGl where the buffer is cleared before writing new pixels to it and perform rasterization.

**`commitTextUpdate`**
Similar to `commitUpdate` but it commits the update payload for the text nodes.

**`removeChild and removeChildFromContainer`**
When we're inside a host component that was removed, it is now ok to remove the node from the tree. If the return fiber (parent) is container, then we remove the node from container using `removeChildFromContainer` else we simply use `removeChild`.

**`insertBefore`**
It is a `commitPlacement` hook and is called when all the nodes are recursively inserted into parent. This is abstracted into a function named `getHostSibling` which continues to search the tree until it finds a sibling host node (React will change this methodology may be in next release because it's not an efficient way as it leads to exponential search complexity)

**`appendChildToContainer`**
如果 fibe 的类型是 `HostRoot` 或 `HostPortal`，那么子组件将被附加到这个容器中。

**`appendChild`**
将子组件添加到父组件中。

**`shouldSetTextContent`**
如果它返回 false 那么 schedule 将重置文本内容。

**`getHostContext`**
It is used to mark the current host context (root instance) which is sent to update the payload and therefore update the queue of work in progress fiber (may indicate there is a change).



**`createTextInstance`**
创建一个 text 节点的实例。

**`supportsMutation`**
`True` ，用于宿主目标中具有类似 DOM 的 `appendChild` 一样的可变 API 的 **mutating renderers** 中。

### 笔记

- 你需要**不要**依赖 Fiber 的数据结构。需要认为它的字段是私有的。
- 将 'internalInstanceHandle' 看做是一个 opaque 对象。
- 使用 host context 从 roots 中获取数据。

> 上述的几点都是在和 [Dan Abramov](https://twitter.com/dan_abramov) 讨论 host config 方法和 Fiber 的属性之后添加到教程当中的。

## 注入第三方 renderers 到 devtools 中

你也可以注入你自己的 renderer 到 react-devtools 来调试你的环境中的组件。以前，第三方的 renderers 是无法完成这样的事情的，但现在使用 `reconciler` 实例的返回值之后，它便可以注入第三方的 renderers 到 react-devtools 中。

**用法**

把 react-devtools 在独立的应用中

```
yarn add --dev react-devtools
```

然后运行

```
yarn react-devtools
```

而如果你使用的是 npm，

```
npm install --save-dev react-devtools
```

然后用下面的方式来运行

```
npx react-devtools
```

```js
const Reconciler = require('react-reconciler');

let hostConfig = {
  // See the above notes for adding methods here
};

const CustomRenderer = Reconciler(hostConfig);

module.exports = CustomRenderer;
```

接着在你的 `render` 方法，

```js
const CustomRenderer = require('./reconciler')

function render(element, target, callback) {
  ... // Here, schedule a top level update using CustomRenderer.updateContainer(), see Part-IV for more details.
  CustomRenderer.injectIntoDevTools({
    bundleType: 1, // 0 for PROD, 1 for DEV
    version: '0.1.0', // version for your renderer
    rendererPackageName: 'custom-renderer', // package name
    findHostInstanceByFiber: CustomRenderer.findHostInstance // host instance (root)
  }))
}
```

那么我们完成了教程的第一部分。我知道仅通过阅读代码来理解一些概念是困难的。开始可能会觉得厌烦，但需要保持尝试，最后会有意义的。我第一次学习 Fiber 架构的时候，我并不能很好的理解所有的东西。虽然挺沮丧的，但我一直通过上述代码中使用 `console.log()` 来帮助自己理解为何如此实现。 "Aha Aha" 的时候，便慢慢出现了。最后它帮助了我构建了 [redocx](https://github.com/nitin42/redocx) 项目。要理解它是有点困惑的，但你最后会明白的。

如果你有任何的疑问，可以打开 DMs ，我在 Twitter 上的 [@NTulswani](https://twitter.com/NTulswani) 。

[More practical examples for the renderer](https://github.com/facebook/react/tree/master/packages/react-reconciler#practical-examples)

[更多关于 renderer 的样例](https://github.com/facebook/react/tree/master/packages/react-reconciler#practical-examples)

[接着是 Part-II](./part-two.ZH-CN.md)
