# 如何构建一个自定义的 React 渲染器

[![Build Status](https://travis-ci.org/nitin42/Making-a-custom-React-renderer.svg?branch=master)](https://travis-ci.org/nitin42/Making-a-custom-React-renderer)

> 让我们一起造一个自定义的 React 渲染器 😎

<p align="center">
  <img src="https://cdn.filestackcontent.com/5KdzhvGRG61WMQhBa1Ql" width="630" height="350">
</p>

## 简介

这是一个教你如果构建一个自定义的 React 渲染器和在你需要的宿主环境中渲染的小教程。该教程分为三个部分 -

- **Part 1** - 构建一个 React reconciler (通过 [`react-reconciler`](https://github.com/facebook/react/tree/master/packages/react-reconciler) 库)。

- **Part 2** - 构建一个实现 reconciler 公共接口 (public interface) 的 "渲染器"。

- **Part 3** - 构建一个支持刷新所有内容到我们需要运行的宿主环境的 render 方法。

## 简要

### [Part-I](./part-one.md)

在第一部分中，我们将使用 [`react-reconciler`](https://github.com/facebook/react/tree/master/packages/react-reconciler) 库来构建一个 React reconciler。同时我们将使用 Fiber 来实现 renderer ，因为 Fiber 拥有构建自定义 renderer 的 first-class 渲染 API。

### [Part-II](./part-two.md)

在第二部分中，我们将为 reconciler 即 renderer 构建一个公共接口(a public interface)。我们也将创建一个自定义的 `createElement` 方法，以及在我们的一些样例中设计组件的 API。

### [Part-III](./part-three.md)

在第三部分中，我们将构建一个 render 方法用来渲染我们的 input 组件。

## 我们怎么构建呢？

我们将构建一个将 React 组件渲染到 word 文档中的自定义渲染器 (renderer)。我已经实现了这部分。源码和文档都存在 [redocx](https://github.com/nitin42/redocx) 项目中。

我们将使用 [officegen](https://github.com/Ziv-Barber/officegen) 来实现功能。下面，我将解析它的一些基本概念。

Officegen 可以生成 Microsoft Office 2007 及之后的版本的 Open Office XML 文件。它会生成的一个输出流而不是文件，与任意输出工具无关。

**创建一个文档对象**

```js
let doc = officegen('docx', { __someOptions__ });
```

**生成一个输出的 stream**

```js
let output = fs.createWriteStream(__filePath__);

doc.generate(output);
```

**事件**

`finalize` - 在流传输完成之后触发

`error` - 错误出现时触发

## 运行该项目

```shell
git clone https://github.com/nitin42/Making-a-custom-React-renderer
cd Making-a-custom-React-renderer
yarn install
yarn example
```

在你运行 `yarn example` 命令之后，将会有一个 docx 文件生成在 [demo](./demo) 目录中。

## 贡献

如果你有更好的建议，欢迎提供给我 😃。

**如果你成功的完成了这个教程, 你可以给这个项目点一个 watch/star 或者关注我的 [twitter](https://twitter.com/NTulswani) 中来获取新的更新。**

<a target='_blank' rel='nofollow' href='https://app.codesponsor.io/link/FCRW65HPiwhNtebDx2tTc53E/nitin42/Making-a-custom-React-renderer'>
  <img alt='Sponsor' width='888' height='68' src='https://app.codesponsor.io/embed/FCRW65HPiwhNtebDx2tTc53E/nitin42/Making-a-custom-React-renderer.svg' />
</a>
