# react-study-notes 学习react小笔记
##一: 定义props数据类型时，用到PropTypes，引入方式参考官网
```js
import PropTypes from 'prop-types';
Counter.propTypes = {
  caption: PropTypes.string.isRequired,
  initValue: PropTypes.number
}
```
##二: 定义的事件，如结构中的点击事件等，必须在构造函数中显式地用.bind方法绑定到实例中，this始终指向当前组件实例
eg:
```js
<button style={buttonStyle} onClick={this.onClickIncrementButton}>+</button>
constructor (props) {
    super(props);
    this.onClickIncrementButton = this.onClickIncrementButton.bind(this);
  }
```
##三: 组件的生命周期
3-1: Mount ,组件第一次在DOM树中渲染的过程
constructor 构造函数，作用：初始化state,绑定成员函数的this环境
getInitialState
getDefaultProps
componentWillMount 基本上不用，放到constructor
render  渲染纯函数
componentDidMount  
3-2: Update，组件被重新渲染的过程
3-3: Unmount，组件从DOM中删除的过程

##四: 更新过程
当props或state被修改时，引发组件的更新过程
更新过程依次执行：
componentWillReceiveProps
shouldComponentUpdate  ＊＊需要有返回结果的函数（布尔值）
componentWillUpdate
render  ＊＊需要有返回结果的函数（构造DOM对象）
componentDidUpdate

注：
componentWillReceiveProps  （nextProps）
只要是父组件render函数被调用，在render函数里面被渲染的子组件就会经历更新过程，不管父组件传给子组件的props有没有改变，都会触发子组件的componentWillReceiveProps函数

shouldComponentUpdate （nextProps, nextState）
决定一个组件什么时候不需要渲染

##五:父子组件间通信
