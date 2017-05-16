---
title: Git相关收集
date: 2017-05-16 17:30:39
tags: 个人整理
categories: 个人整理
---

乘着改革开放的浪潮，这段时间我们终于接触到非常火热的前端项目构架React+Redux。

这个构架下的前端项目，最大的优点就是Redux鼓励各个组件无状态化(no state)，利用store统一管理state，从而使各个组件之间相对更加独立和易于维护，使得前端的构架更加简单化。下图中左边是经典React中各组件的层级关系，右边是引入Redux之后的层级。在左图中，当修改父节点/组件时，子组件也可能会被破坏掉；而右图中能够影响到各个组件的因素只有state。
![](http://upload-images.jianshu.io/upload_images/1744544-dcfd2a455d06f188.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在传统JS Web项目中的自动化测试，通常会有这些比较突出的问题
- UI自动化功能测试受制于环境(运行os,浏览器等)维护困难，运行缓慢，而且非常容易因为前端变化而被破坏。
- 单元测试覆盖点有限，无法覆盖所有的测试点。

那么作为新技术的React+Redux的出现，会不会给也测试带来一些新的思路或者机会来解决这些问题呢？比如放弃掉经典的UI自动化测试？

作为浸泡在测试金字塔理论中多年的吃瓜的测试群众，我对越低层的测试成本更少、反馈问题更快这个道理深以为然。所以在面对这样的新鲜事物的时候，总愿意去分析下是否可以结合项目的技术特点，尽量把自动化测试往低层移，减少成本，加速反馈周期。还可以把锅甩给研发同学。

# 分析可行性
为了分析技术上实现的可行性，我们至少需要知道React和Redux的一些基本概念：
- Store : 全局唯一的对象，用来保存state
- State : 某个时间点上state的快照，和改时间点上的view应该是一一对应的
- Action : view 通过store.dispatch(action)发出的通知，表示 state 应该要发生变化了。
- Reducer : 接受action和当前state，返回新的state的函数
- UI Component : 纯负责显示UI，无状态
- Container (Component): 负责一些业务逻辑和connect UI组件
- Provider : React-Redux库的让react组件拿到新的state的方法

还需要了解Redux大致的工作流程：
1. 用户操作view触发action
2. store被action通知state要变化了，调用reducer
3. reducer计算新的 state应该是啥样，返回新的state给store
4. store通过react组件把新的state对应的view显示给客户

我刚好写了个简单的[demo](https://github.com/xuxtc/react-redux-demo)，能大概看懂这个demo中，各个组件是做什么的，如何工作的，对后面的内容有极大帮助。demo使用了webpack作为打包和本地运行工具

这么看起来，redux通过state-view一一对应的架构保证了只要view变，state一定变，反之亦然。这种一一对应的关系减少了组件之间发生关联(变化)的可能性，从而减轻了测试复杂度。最后再总结一下，发现针对该架构的自动化测试其实只需要保证下面几点就够了：
- 各个单独组件能够正常显示DOM元素
- 如果state改变了, 那么我只需要确保相应的view发生了变化
- 如果view发生了改变, 那么我只需要确保相应的state存进了store

# 验证分析结果
从上面儿的分析结果来看，只用低层测试来保证质量的想法，好像有点儿靠谱的样子。接下来就是做一些小的demo来验证下真实项目中是否行得通。

好的单元测试应该有哪些特点呢？
- 简单易懂。最好是BDD风格的，一眼就可以看出你在测试什么，减少维护成本
- 高覆盖率，研发重构的时候会更有信心
- 跑的快，不要有额外的工作(例如维护复杂的环境依赖等)
- 从客户价值(business value)角度出发，确保软件的可交付性。

那么首先，我们应该是尽量选择一款满足上面需求的测试工具。

#### 测试工具选择
满足上面条件的JS前端单元测试工具/框架很多，比较流行的是mocha+chai、JEST等。这里我们使用[JEST](http://facebook.github.io/jest/)测试框架和[Enzyme](http://airbnb.io/enzyme/index.html)测试工具库。

经过实际使用后发现，JEST对比Mocha来说，虽然运行速度上感觉比Mocha稍慢，但是因为如下几个优点最后胜出：

- 和React师出同门，FB官方支持
- 已经集成了测试覆盖率检查、mock等功能，不需要安装额外的库
- 文档完备，官方提供了和babel、webpack集成情况下以及异步调用的测试解决方案
- 官方提供snapshot testing解决方案

#### 安装
JEST 和 Enzyme 官方提供了详细的安装指导，实际安装完成后发现还是有坑。这里把安装过程重新梳理下。

首先是JEST
```
npm install --save-dev jest
```
如果需要在测试项目中使用`babel`，还需要额外安装`babel-jest`,
```
npm install --save-dev babel-jest
```
然后是Enzyme,
```
npm install enzyme --save-dev
```
如果使用的是react13以上的版本，则需要额外安装`react-addons-test-utils`
```
npm i --save-dev react-addons-test-utils
```

#### 配置
安装完成后，就可以开始写测试啦~
不要方！JEST运行基础功能虽然无需配置，但是官方依然提供了配置选项来实现个性化需求。

例如，在单元测试覆盖率检查的时候，默认只检查被测试文件所使用到的源文件的覆盖率。然而，我们可以通过在package.json文件中配置jest的collectCoverageFrom参数，来指定检查所有需要测试的文件(无论源文件有没有被测试文件使用到）

以上面提到的demo为例。我们需要确定单元测试的范围--目标测试的文件是`src`文件夹下面的`.jsx`或者`js`文件，同时需要忽略其中的一些配置性质的`jsx/js`，比如`store.js`、`provider.jsx`和用于合并`reducer`的`index.js`。另外，还有覆盖率检查的时候生成`coverage`文件夹下面的js，编译后在`dist`文件夹下面生成的`js`文件，以及`webpack`的`config`文件都不需要测试。那么我们就在`package.json`里面加上这样一段内容
```
"jest": {
    "collectCoverageFrom" : [
      "**/*.{js,jsx}",
      "!**/coverage/**",
      "!**/dist/**",
      "!**/store.js",
      "!**/provider.jsx",
      "!**/index.js",
      "!**/webpack.config.js"
    ]
  }
```
然后给我们单元测试的覆盖率定个小目标，95%吧。只有当测试覆盖率大于等于这个比例的时候测试才会通过。
```
"jest": {
  "collectCoverageFrom" : [
    "**/*.{js,jsx}",
    "!**/coverage/**",
    "!**/store.js",
    "!**/provider.jsx",
    "!**/index.js",
    "!**/webpack.config.js"
  ],
  "coverageThreshold": {
    "global": {
      "branches": 95,
      "functions": 95,
      "lines": 95,
      "statements": 95
    }
  }
}
```
JEST配置选项有很多有用的功能，例如指定加载启动文件、指定moduleNameMapper、指定别名等。[详见这里](http://facebook.github.io/jest/docs/configuration.html)。

最后，我们还要给执行JEST加上一个命令：在package.json文件的scripts区域中增加一句
```
"scripts": {
  ...
  "test": "jest"
}
```
这样，我们就可以通过 `npm test` 命令来跑测试了。

#### 实现单元测试
工具准备完成，就可以开始写测试啦~
不要方！我们知道Redux基本概念中的store等组件的功能和目的各不相同，那么针对各种组件的特性，我们分别应该如何测试呢？翻看Redux官网，发现这里有详细的例子和介绍，附上[官网传送门](http://redux.js.org/docs/recipes/WritingTests.html)。

需要注意的是，`UI Component`由于无状态化，和只负责显示DOM的作用，所以针对它们的单元测试只需要验证是否按预期显示了DOM就行了。组件中的`props`、方法等则无需测试。

还是以demo中的代码为例子。我们`footer.jsx`组件的代码是酱紫的：
```
import React from 'react'


export default class Footer extends React.Component {
  constructor() {
    super()
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick(){
    this.props.onClick()
  }

  render(){
    return(
      <div style={styles.base}>
        <footer>
          <a onClick={this.handleClick}>click footer to back</a>
        </footer>
      </div>
      )
  }

}

Footer.propTypes = {
  onClick: React.PropTypes.func.isRequired
}

```
其中`render()`是React中负责显示DOM的代码, `handleClick()`是一个自定义方法，`onClick`则是一个`props`。再来看看测试代码`footer.test.js`
```
import React from 'react'
import { shallow } from 'enzyme'

import Footer from '../../src/components/UIs/footer'
const props = {
  onClick: jest.fn()
}

describe('Footer component', () => {
  it('should render dom', () => {
    const wrapper = shallow(<Footer {...props}/>)
    expect(wrapper.find('a').text()).toContain('click footer')
  })
})

```
测试代码中使用了enzyme库中的shallow功能。shallow是官方测试工具库react-addons-test-utils中shallow rendering的封装。是将一个组件渲染成虚拟DOM对象的“浅渲染”。这种渲染不会涉及子组件，不需要DOM，速度非常快。

源代码中onClick后调用的方法，在这里被JEST自带的mocked方法jest.fn()代替掉了，我们这个测试只测试了组件是否被正常显示出来了。expect部分是断言，实现内容是在被渲染出的footer组件中找到a标签，然后断言它的text()中有没有包含期望的文字。通过这种方式我们可以得知组件是否有被显示出来。

除了text()属性以外，还可非常灵活的通过其他方式来得知组件是否被正常显示。例如：

```
expect(wrapper.find('button').exists()).toBeTruthy()
expect(wrapper.find('input').props().type).toBe('text')
```
前者是断言被渲染出的组件中是否有button标签的存在；后者是断言组件中的input标签是否有type="text"这个属性。

针对各个action、reducer和UI Component的测试写完成后，我们来运行下测试，查看覆盖率。
> Tips: 可以通过npm test <测试文件名> 运行单个测试
![](http://upload-images.jianshu.io/upload_images/1744544-3b5c0892abd54953.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个时候我们就看到了之前配置的测试覆盖率检查范围的作用了：报告明确告诉了我们，app.jsx没有被测试到。另外，在footer.jsx中还有第19行以及userName.jsx第34行也没有被测试到，覆盖率一片红..

检查了下未被覆盖的footer 19行和userName 34行，发现正是之前特意忽略掉的UI Component内的方法。app.jsx是一个Container组件，还没有写任何测试。

#### 实现功能测试
发现问题了，那就赶紧补测试吧~
不要方！我们先来仔细分析下。

- 容器(container)组件的主要作用是链接UI组件，里面可能也包含了一些业务逻辑
- UI Component中的方法，最终会通过容器组件对组件的调用而被调用到。

那么换句话说，我只需要按照功能测试的方法，以user journey的角度来测试这个组件，就可以覆盖到所有东西咯？再拿demo来练练手。

先确定demo的功能和user journey是：

- 用户输入任意字符，输入的同时，会在下方显示出输入的值
- 点击Submit按钮后提交form，更新界面显示
- 点击footer后可以回到首页

![](http://upload-images.jianshu.io/upload_images/1744544-f69556cc09f3b597.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/1744544-ffd3d147d7f2a796.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么我们测试的内容就应该是：
```
import React from 'react'
import { createStore } from 'redux'
import { mount } from 'enzyme'
import { Provider } from 'react-redux'
import ReactDom from 'react-dom'

import App from '../../src/components/app'
import Reducer from '../../src/reducers'

let store
let wrapper

const fillin = (byCssSelector, text) => {
  wrapper.find(byCssSelector).simulate('change', {target: {value:text}})
}

beforeEach( () => {
  store = createStore(Reducer)
  wrapper = mount (
    <Provider store={store}>
    <App />
    </Provider>
  )
})

describe('User journey', ()=> {
  describe('user input a string in the field', ()=> {
    it('should display inputed name', ()=> {
      fillin('#userName', 'Han mei 妹@')
      expect(wrapper.find('#userInput').text()).toContain('Han mei 妹@')
    })
  })

  describe('click submit button', ()=> {
    it('should show welcome page, and original view disappear', ()=> {
      fillin('#userName', '李磊@_@')
      wrapper.find('form').simulate('submit')
      expect(wrapper.find('#welcome').text()).toContain('李磊@_@')
      expect(wrapper.find('#userInput').exists()).toBeFalsy()
    })
  })

  describe('click footer to back', ()=> {
    it('should show welcome page, and original view disappear', ()=> {
      fillin('#userName', 'linTao123')
      wrapper.find('form').simulate('submit')
      wrapper.find('a').simulate('click')
      expect(wrapper.find('#userInput').exists()).toBeTruthy()
    })
  })
})
```
simulate方法是enzyme封装好的模拟页面元素事件的方法，用来模拟`"click","submit","change","doubeClick"`等事件。

测试代码中的fillin方法实现的是在渲染后的DOM中找到一个web element，然后用simulate方法模拟绑定在该元素上面的onChange()事件。

beforeEach方法是一个JEST的Hook，在每一个it/test开头的测试之前都会执行里面的内容。在demo的案例中，我把store.js和provider.js里面的内容照搬了过来，每个测试执行前都会新生成一个store，实现重置store的功能（重置测试环境）。

功能测试看上去也没问题了，所有的用户场景貌似都覆盖完了。这个时候我们再来检查下覆盖率

```
npm test -- --coverage
```
![](http://upload-images.jianshu.io/upload_images/1744544-95f196802e1c8dd0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

100%覆盖率了有木有！完美啊有木有！
为何加上功能测试以后，之前UI组件里面没有测试到的方法也被覆盖到了呢？分析下产品代码，原来是在页面元素上执行操作的时候，就会调用到UI组件上的这些方法，而这些操作后来被功能测试覆盖到了。

这么一来，又避免了重复的测试代码 :）

> 引自： https://testerhome.com/topics/8032