---
title: Git相关收集
date: 2017-05-16 15:20:39
tags: 个人整理
categories: 个人整理
---

构建和共享组件是React最强大的功能之一，但是没有任何文档，我们的组件有什么好处？

我最近参与了一个项目，创建了将被广泛开发人员采用的组件，并且已经学会了一种方便自动化组件文档的方法

在这篇文章中，我们将介绍为什么您可能需要记录您的组件，以及如何使用React轻松实现。

# 为什么要做文档组件？
那么首先你可能会想，为什么我们需要记录我们的组件？
说实话，这真的取决于你正在建造什么，谁将会消耗你的组件。
在我们的测试中构建Web应用程序时，可能会为我们的组件提供足够的文档。
但是，如果您在整个业务或在线社区中使用大量应用程序或大量组件，那么文档就是关键。
如果我们看一些例如`React Bootstrap`或`Material UI`，他们的文档真的可以帮助开发人员在几分钟之内完成设置并利用它们的组件。
从这些项目中吸取灵感，我想为我的项目做类似的事情，并设法使用我在路上找到的一些令人敬畏的工具。

所以我们来看看他们。

# react-docgen
`react-docgen`是一个CLI和工具箱，用于从React组件中提取信息，并从中生成文档。

这个想法很简单 我们将组件传递给反应文件，它将返回一个对象。

让我们来看看这个反馈文档[GitHub页面上的例子](https://github.com/reactjs/react-docgen)。

##### Component Example
```
var React = require('react');

/**
 * General component description.
 */
var Component = React.createClass({
  propTypes: {
    /**
     * Description of prop "foo".
     */
    foo: React.PropTypes.number,
    /**
     * Description of prop "bar" (a custom validation function).
     */
    bar: function(props, propName, componentName) {
      // ...
    },
    baz: React.PropTypes.oneOfType([
      React.PropTypes.number,
      React.PropTypes.string
    ]),
  },

  getDefaultProps: function() {
    return {
      foo: 42,
      bar: 21
    };
  },

  render: function() {
    // ...
  }
});

module.exports = Component;
```
##### JSON output
```
{
  "props": {
    "foo": {
      "type": {
        "name": "number"
      },
      "required": false,
      "description": "Description of prop \"foo\".",
      "defaultValue": {
        "value": "42",
        "computed": false
      }
    },
    "bar": {
      "type": {
        "name": "custom"
      },
      "required": false,
      "description": "Description of prop \"bar\" (a custom validation function).",
      "defaultValue": {
        "value": "21",
        "computed": false
      }
    },
    "baz": {
      "type": {
        "name": "union",
        "value": [
          {
            "name": "number"
          },
          {
            "name": "string"
          }
        ]
      },
      "required": false,
      "description": ""
    }
  },
  "description": "General component description."
}
```
我们返回的JSON对象已经从我们的组件中提取了文档。

对象包含我们在组件中定义的所有属性。

对于每个prop的返回类型，如果必须，说明和任何默认值。

所以使用CLI是好的，但是我们如何在React应用程序中使用它并自动化进程？ 它实际上很直接，所以让我们来看看。

# 如何在React中使用react-docgen
首先, 安装 `react-docgen`
```
npm install --save react-docgen
```
接下来, 导入 `react-docgen`
```
import {parse} from 'react-docgen';
```
现在我们可以解析任何组件并获取文档。 但是要使用解析函数，我们需要将组件转为`string`。

所以我们可以使用`raw-loader`将一个组件作为一个字符串导入
#### 安装raw-loader
```
npm install raw-loader
```
在使用webpack设置raw-loader后，我们可以以原始格式导入任何文件。
以原始格式导入文件
```
import Component from '!raw!./Component';
```
> 注意：组件将作为字符串而不是React组件返回给我们。
使用组件作为字符串，我们可以将它从反馈文档传递给我们的解析函数，以获取一些文档

```
import {parse} from 'react-docgen';
import Component from '!raw!./Component';

const componentDocs = parse(Component);
```
在这个示例中，componentDocs将组件文档保存为JSON对象。
#### 这是什么意思？
对于能够从我们的组件获取文档的大规模的React应用程序可能是一个好主意。

使用`react-docgen`和`raw-loader`，我们可以轻松创建一个简单的用户界面来表示我们的组件库及其文档，供其他开发人员使用。

使用这些信息，我们可以直观地显示我们使用的组件，我们的组件道具细节和组件描述。

这是一个我一直在做的工作的例子，你可以通过`react-docgen`来实现。

Basic React应用程序，用于呈现组件文档
![](http://www.davidboyne.co.uk/images/docs.png)

Button的描述和道具在组件更改时自动生成，确保文档始终保持最新的其他开发人员。

这个帖子的灵感来自于在GitHub上`material-ui`项目。

在GitHub上检查`material-ui`项目，了解如何使用此技术来帮助驱动其组件文档的一些很棒的例子。

如果您有任何问题[告诉我](https://twitter.com/boyney123)，或发表评论。

> 1. 本文译自: http://www.davidboyne.co.uk/2016/05/26/automating-react-documentation.html
> 2. 另一篇文档参考: http://www.seekjune.com/post/8426