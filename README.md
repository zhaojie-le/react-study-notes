# react-study-notes 学习react小笔记
## 一: 定义props数据类型时，用到PropTypes，引入方式参考官网
```js
import PropTypes from 'prop-types';
Counter.propTypes = {
  caption: PropTypes.string.isRequired,
  initValue: PropTypes.number
}
```
## 二: 定义的事件，如结构中的点击事件等，必须在构造函数中显式地用.bind方法绑定到实例中，this始终指向当前组件实例
eg:
```js
<button style={buttonStyle} onClick={this.onClickIncrementButton}>+</button>
constructor (props) {
    super(props);
    this.onClickIncrementButton = this.onClickIncrementButton.bind(this);
  }
```
## 三: 组件的生命周期
### 3-1: Mount ,组件第一次在DOM树中渲染的过程
* constructor 构造函数，作用：初始化state,绑定成员函数的this环境
* getInitialState
* getDefaultProps
* componentWillMount 基本上不用，放到constructor
* render  渲染纯函数
* componentDidMount   dom渲染完成后，获取数据
### 3-2: Update，组件被重新渲染的过程
### 3-3: Unmount，组件从DOM中删除的过程

## 四: 更新过程
当props或state被修改时，引发组件的更新过程
更新过程依次执行：
* componentWillReceiveProps
* shouldComponentUpdate  ＊＊需要有返回结果的函数（布尔值）
* componentWillUpdate
* render  ＊＊需要有返回结果的函数（构造DOM对象）
* componentDidUpdate

注：
* 1: componentWillReceiveProps  （nextProps）
只要是父组件render函数被调用，在render函数里面被渲染的子组件就会经历更新过程，不管父组件传给子组件的props有没有改变，都会触发子组件的componentWillReceiveProps函数

* 2: shouldComponentUpdate （nextProps, nextState）
决定一个组件什么时候不需要渲染

## 五:父子组件间通信
### 1: 父－－>子 传參
参考demo中example-base
子组件中定义一个变量caption变量，需要从父组件中传递到子组件中
* 子组件中
```js
import React, { Component } from 'react';
// 引入传递参数定义属性
import PropTypes from 'prop-types';
// 实例一个组件
class Counter extends Component {
  constructor (props) {
    // super(props)调用父组件React.Component的构造函数，若没有该行代码，则子组件实例后，无法通过this.props访问到父组件传递的props值
    super(props);
    // 定义组件内使用的变量
    this.state = {
      count: 0
    }
  }
  // 触发像父组件传递变量的方法
  changeProps () {
    let a = '传递参数a'
    let b = '传递参数b'
    this.props.onUpdate(a, b)
  }

  render () {
    // 通过this.props访问传入的参数值
    const {caption} = this.props
    return (
      <div>{caption}</div>
    )
  }
}
// 在子组件中定义需要从父组件传递的参数，中间string代表参数的类型，后面的isRequired定义是必须传递参数
Counter.propTypes = {
  caption: PropTypes.string.isRequired,
  onUpdate: PropTypes.func
}
// 父子组件通信的函数，可以将子组件的参数传递给父组件，供父组件处理
Counter.defaultProps = {
  onUpdate: f => f   // 默认是一个什么都不做的函数
}
export default Counter
```
* 父组件
```js
import React, { Component } from 'react';
import Counter from './Counter'  // 引入子组件模块
class ControlPanel extends Component {
  constructor (props) {
    super(props)
    // 子组件中传递到父组件函数onUpdate绑定的当前事件绑定到实例中
    this.onCounterUpdata = this.onCounterUpdata.bind(this)
  }
  render () {
    return (
      <div>
        <Counter onUpdate={this.onCounterUpdata} caption="First"></Counter>
      </div>
    )
  }
}
export default ControlPanel
```
* 同vue的区别
事件需要显式的声明绑定到实例上，通过.bind，将this指定到当前的实例中
## 六：Flux的实践－参考demo中example-flux中例子
## 七：Redux的学习
### redux的基础应用－参考demo中expample-redux
### redux的进一步，创建全局context 参考demo中example-redux-context
### react-redux的使用，参考demo中example-react-redux
### 三个基本原则
* 唯一数据源
应用的状态数据应该只存储在唯一的一个Store上，整个应用只保存一个Store，所有组件的数据源就是这个Store上的状态

* 保持状态可读
不能直接去修改状态，要修改Store的状态，必须要通过派发一个Action对象来修改Store状态

* 数据改变只能通过纯函数完成
## 八 模块化react和redux的应用
### 第一步创建一个复杂的应用
* 模块化应用的要点
* 代码文件的组织方式
* 状态树的设计
* 开发辅助工具
#### 模块化应用要点
* 代码文件的组织结构
* 确定模块的边界
* Store的状态树设计
## 九 异步数据请求
在react渲染生命周期中，组件的componentDidMount函数中异步请求数据最为合理
### 开发过程中的接口代理
本地开发过程中，往往借口涉及到跨域访问，若用react-create-app创建项目，可以使用根目录下package.json中的
```js
"proxy":"http://www..." 
```
该应用接收到不是要求本地资源的HTTP请求时，这个请求的协议和域名会替换成proxy中的部分，转手发送该请求，并将接收到的结果返还给浏览器，这样就实现了代理功能
### redux管理异步数据
访问服务器的action，至少涉及三个action类型：
* 表示异步操作已经开始的action,表示请求信息的请求已经发送给服务器的状态－异步操作正在进行状态
* 表示异步操作成功的action－异步操作已经成功完成
* 表示异步操作失败的action－异步操作已经失败
当上述三种类型的action被派发时，会让React组件进入各自不同的三种状态