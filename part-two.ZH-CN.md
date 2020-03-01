# Part-II

在[上一节](./part-one.ZH-CN.md)中，我们已经构建了一个 React reconciler 并解析了它如何管理我们的 renderers 的生命周期。

在第二节中，我们将创建一个 reconciler 的公共接口 (public interface)。我们也将设计我们自己组件的 API 和如果构建一个自定义版的 `createElement` 方法。

## 组件

在我们的例子中，我们将只实现一个 `Text` 组件。 `Text` 组件是用来添加文本到我们的文档中的主要组件。

> Text 组件, 无论如何, 都不可以创建特定的 text 节点. 和 DOM API 相比，它拥有不同的语义。

We will first create a root container (remember `rootContainerInstance` in our reconciler ?) for our components. This is responsible
for creating a document instance for [officegen](https://github.com/Ziv-Barber/officegen).

首先我们将为我们的组件创建一个 root 容器 （还记得在我们的 reconciler 的 `rootContainerInstance` ？）。这是基于 [officegen](https://github.com/Ziv-Barber/officegen) 来创建一个文档的实例。

**`WordDocument.js`**

```js
import officegen from 'officegen';

// This creates the document instance
class WordDocument {
  constructor() {
    this.doc = officegen('docx');
  }
}

export default WordDocument;
```

现在，让我们来创建 `Text` 组件。

**`Text.js`**

```js
class Text {
  constructor(root, props) {
    this.root = root;
    this.props = props;

    this.adder = this.root.doc.createP();
  }

  appendChild(child) {
    // Platform specific API for appending child nodes
    // Note: This will vary in different host environments. For example - In browser, you might use document.appendChild(child)
    if (typeof child === 'string') {
      // Add the string and render the text node
      this.adder.addText(child);
    }
  }
}

export default Text;
```

Let's see what's going on here!

**`constructor()`**

In our `constructor`, we initialise the `root` instance and `props`. We also create a reference to our `doc` instance which we created earlier in `WordDocument.js`. This reference is then
use to create paragraph by adding text nodes to it.

Example -

```
this.adder.addText(__someText__)
```

**`appendChild`**

This method appends the child nodes using the platform specific function for `docx` i.e `appendChild`. Remember we used this in our reconciler's `appendInitialChild` method to check whether the
parent instance has a method called `appendChild` or not !?

```js
appendInitialChild(parentInstance, child) {
	if (parentInstance.appendChild) {
		parentInstance.appendChild(child);
	} else {
		parentInstance.document = child;
	}
}
```

Along with `appendChild` method, you can also add `removeChild` method to remove child nodes. Since our host target does not provide a mutative API for removing child nodes, we are not using this method.

> For this tutorial, the scope is kept limited for `Text` component. In a more practical example, you might want to validate the nesting of components too.

#### 笔记

- Do not track the children inside an array in your class component API. Instead, directly append them using specific host API, as React provides all the valuable information about the child (which was removed or added)

This is correct

```js
class MyComponent {
  constructor(rootInstance, props) {
    this.props = props;
    this.root = rootInstance;
  }

  appendChild(child) {
    some_platform_api.add(child);
    // In browser, you may use something like: document.appendChild(child)
  }
}
```

这是不对的

```js
class MyComponent {
  children = [];

  constructor(rootInstance, props) {
    this.props = props;
    this.root = rootInstance;
  }

  appendChild(child) {
    this.children.push(child);
  }

  renderChildren() {
    for (let i = 0; i < this.children.length; i++) {
      // do something with this.children[i]
    }
  }

  render() {
    this.renderChildren();
  }
}
```

- If you're rendering target does not provide a mutate method like `appendChild` and instead only lets you replace the whole "scene" at once, you might want to use the "persistent" renderer mode instead. Here's an [example host config for persistent renderer](https://github.com/facebook/react/blob/master/packages/react-native-renderer/src/ReactFabricHostConfig.js).

## createElement

这是一个和 `React.createElement()` 类似的用于创建 DOM 的方法。

**`createElement.js`**

```js
import { Text, WordDocument } from '../components/index';

/**
 * Creates an element for a document
 * @param {string} type Element type
 * @param {Object} props Component props
 * @param {Object} root Root instance
 */
function createElement(type, props, root) {
  const COMPONENTS = {
    ROOT: () => new WordDocument(),
    TEXT: () => new Text(root, props),
    default: undefined,
  };

  return COMPONENTS[type]() || COMPONENTS.default;
}

export { createElement };
```

我想你已经可以理解在 `createElement` 方法中发生了什么事。它需要一个元素及其属性和 root 实例。

Depending upon the type of element, we return an instance based on it else we return `undefined`.

We're done with the part two of our tutorial. We created the API for our two components (`Document` and `Text`) and a `createElement` method to create an element. In the next part, we will create a render method to flush everything to the host environment.

[接着是 Part-III](./part-three.ZH-CN.md)
