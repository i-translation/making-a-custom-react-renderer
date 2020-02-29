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

In part two, we will create a public interface to the reconciler i.e a renderer. We will create a custom method for `createElement` and will also architect the component API for our example.

在第二部分中，我们将为 reconciler 即 renderer 构建一个公共接口(a public interface)。

### [Part-III](./part-three.md)

在第三部分中，我们将构建一个 render 方法用来渲染我们的 input 组件。

## What we will build?

We will create a custom renderer that will render a React component to a word document. I've already made one. Full source code and the documentation for that is available [here](https://github.com/nitin42/redocx).

We will use [officegen](https://github.com/Ziv-Barber/officegen) for this. I'll explain some of it's basic concepts here.

Officegen can generate Open Office XML files for Microsoft Office 2007 and later. It generates a output stream and not a file.
It is independent of any output tool.

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

在你运行 `yarn example` 命令之后，将会有一个 docx 文件生成在 [demo](./demo) 目录中

## 贡献

Suggestions to improve the tutorial are welcome 😃.

**If you've completed the tutorial successfully, you can either watch/star this repo or follow me on [twitter](https://twitter.com/NTulswani) for more updates.**

<a target='_blank' rel='nofollow' href='https://app.codesponsor.io/link/FCRW65HPiwhNtebDx2tTc53E/nitin42/Making-a-custom-React-renderer'>
  <img alt='Sponsor' width='888' height='68' src='https://app.codesponsor.io/embed/FCRW65HPiwhNtebDx2tTc53E/nitin42/Making-a-custom-React-renderer.svg' />
</a>
