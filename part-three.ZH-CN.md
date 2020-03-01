# Part-III

这是我们这个教程的最后一节。我们已经完成了所有繁琐的工作，创建一个 React reconciler ，实现我们自己的 reconciler 的公共接口 (public interface), 和设计组件 API。

那么我们现在只需要构建 `render` 方法来刷新数据到宿主环境中。

## render

```js
import fs from 'fs';
import { createElement } from '../utils/createElement';
import { WordRenderer } from './renderer';

// renders the component
async function render(element, filePath) {
  const container = createElement('ROOT');

  const node = WordRenderer.createContainer(container);

  WordRenderer.updateContainer(element, node, null);

  const stream = fs.createWriteStream(filePath);

  await new Promise((resolve, reject) => {
    container.doc.generate(stream, Events(filePath, resolve, reject));
  });
}

function Events(filePath, resolve, reject) {
  return {
    finalize: () => {
      console.log(`✨  Word document created at ${path.resolve(filePath)}.`);
      resolve();
    },
    error: () => {
      console.log('An error occurred while generating the document.');
      reject();
    },
  };
}

export default render;
```

让我们看看这里发生了什么！

**`container`**

这是 root 实例（还记得在 我们的 reconciler 中的 `rootContainerInstance` 吗？）。

**`WordRenderer.createContainer`**

该函数获取一个 `root` 容器和返回当前的 fiber (flushed fiber)。记住 fiber 是一个包含了组件信息的 JavaScript 对象。

**`WordRenderer.updateContainer`**

该函数通过获取一个元素，一个 root 容器，和父组件，回调函数，并调度顶层的更新。通过当前的 fiber 和优先级（取决于所处的上下文）来完成对应的调度更新。

最后，我们渲染所有子组件和通过创建一个写入流来生成 word 文档。

如果仍有一些疑问？可以查看 [FAQs](./faq.md) 。

耶！你已经成功完成了本教程。在本仓库 ([src](./src)) 目录中已经提供了本教程的完整源码。如果你想要阅读整个源码，可以遵循下面的顺序 -

[`reconciler`](./src/reconciler/index.js) => [`components`](./src/components/) => [`createElement`](./src/utils/createElement.js) => [`render method`](./src/render/index.js)

如果你喜欢阅读本教程，请 watch/star 本项目， 也可以关注我的[Twitter](http://twitter.com/NTulswani) 来获取最新消息。

感谢你阅读本教程！
