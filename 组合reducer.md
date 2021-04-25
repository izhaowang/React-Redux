###
 两个功能模块的reducer。进行合并
 reducer.createStore() 接收一个reducer， 对于多个reducer，可以进行组合，成为一体；
 ```
 import {createStore, combineReducer} from 'redux';
 
 import {reducer as todoReducer} from './todos';
 import {reducer as filterReducer} from './filter';
 
 // combineReducers 接受一个对象作为参数， 参数对象的每个字段名对应了 State状态上的字段名； 返回一个新的reducer
 const reducer = combineReducers({
  todos: todoReducer,
  filter:filtereReducer
 })
 export default createStore(reducer);
```



### PropTypes使用 用于外界传入组件的数据交互
```
improt React {Component, PropTypes} from 'react';

class Counter extends Component {
 constructor(props) {
  super(props)
 }
 render() {
  return(
   <button onClick={increase}>+</button>
   <span>值：{value}</span>
   <span>总数:{sum}</span>
  )
 }
}


Counter.propTypes = { // 传给Counter的属性
 increase: PropTypes.func.isRequired,
 value: PropTypes.string.isRequired,
 sum: PropTypes.number
}

Counter.defaultProps =  {
 sum: 0 // 默认值
}
```


this.increase = this.increase.bind(this);
this.decrease = this.increase;
如果不绑定this
如果类定义中没有绑定this的指向,当用户的点击动作触发this.increase( )这个方法时，实际上执行的是原型方法，可这样看起来并没有什么影响，如果当前组件的构造器中初始化了state这个属性，那么原型方法执行时，this.state会直接获取实例的state属性，如果构造其中没有初始化state这个属性（比如React中的UI组件），说明组件没有自身状态，此时即使调用原型方法似乎也没什么影响。

事实上的确是这样，这里的bind(this)所希望提前规避的，就是著名的this指针丢失的问题。

例如使用解构赋值的方式获取某个属性方法时，就会造成引用转换丢失this的问题:

const toggleButton = new ToggleButton();

import {handleClick} = toggleButton;
严格模式下 this 不是指向window 而是指向undefined
上例中解构赋值获取到的handleClick这个方法在执行时就会报错，Class的内部是强制运行在严格模式下的，此处的this在赋值中丢失了原有的指向，在运行时指向了undefined，而undefined是没有属性的。

另一个存在的限制，是没有绑定this的响应函数在异步运行时可能会出问题，当它作为回调函数被传入一个异步执行的方法时，同样会因为丢失了this的指向而引发错误。





### ract组件完成两件事
1. 和redux Store 打交道， 读取Store 的状态， 用于初始化组件的状态，同事还监听Store状态改变；当Store状态发生改变时， 需要更新组件状态，从而重新渲染； 当需要更新store时， 进行派发actions 对象
2. 根据当前props和state， 渲染出界面

根据以上两个任务，我们可以把一个组件进行拆分为外层容器组件和内层展示组件： 
关于展示组件： 它是一个无状态组件， 只接受props 从而展示内容界面；没有state，也不和Store打交道
```
function Counter({captions, propIncrease, propDecrease, count}) { // 精简后的内层展示组件
    return (
        <div>
            <button style={buttonStyle} onClick={propIncrease}>+</button>
            <button style={buttonStyle} onClick={propDecrease}>-</button>
            <span>{captions}: {count}</span>
        </div>
    )
}
Counter.propTypes = {
    captions: PropTypes.string.isRequired,
    propIncrease: PropTypes.func.isRequired,
    propDecrease: PropTypes.func.isRequired,
    count: PropTypes.number.isRequired
}
```



关于外层容器组件： 承担了所有和Store关联的工作， 它的render所做的就是 赋值传递prop 和 渲染内层组件
也就是1. 把Store 上的状态转化为 内层展示组件的prop
     2.  把内层展示组件用户的动作派发给Store
```
class CounterContainer extends Component{ // 外层容器组件
    constructor(props) {
        super(props)
        console.log(this)
        this.onIncrease = this.onIncrease.bind(this);
        this.onDecrease = this.onDecrease.bind(this);
        this.onChange = this.onChange.bind(this)
        // this.state = { // 初始化state
        //     count: props.initValue || 0
        // }
        this.state = this.getOwnState(); // 初始化了state
    }
    ....
    onIncrease() {
        console.log(store)
        debugger
        store.dispatch(Action.increasement(this.props.captions)) // 手动除非increase 返回 {type：'increase', caption: this.props.captions}
    }
    onDecrease() {
        store.dispatch(Action.decreasement(this.props.captions))
    }
    onChange() {
        this.setState(this.getOwnState())
    }
    componentDidMount() { // 完成挂载后进行监听 store里面的数据变化
        store.subscribe(this.onChange)
    }
    componentWillUnmount() {
        store.unsubscribe(this.onChange)
    }
    render() { 
        return <Counter captions={this.props.captions}
        propIncrease={this.onIncrease}
        propDecrease={this.onDecrease}
        count={this.state.count}
        />
    }
}

    // 该模块最终向外暴露外层组件
```


### 关于外层容器组件 1. 把Store 上的状态转化为 内层展示组件的prop   2.  把内层展示组件用户的动作派发给Store
这两个工作的套路也很明显 把Store的状态转化为内层组件的prop 其实就是映射关系，去掉框架，就是一个mapStateToProps 函数该做的事情，
这个函数把state中的值， 发送给内容组件props
1.1. 把Store 上的状态转化为 内层展示组件的prop
const mapStateToProps(state, ownProps) {
    return {
        count: state[ownProps.caption] // 这个ownProps 直接传递给外层容器组件的props
    }
}

2. 把内层展示组件用户的动作派发给Store
const mapDispatchToProps(dispatch, ownProps) { // 这个ownProps 直接传递给外层容器组件的props
     onIncrease:() => { // 代替外层组件的 onIncrease
         dispatch({type: 'increase', caption: 'first'})
     },
     onDecrease:() => { // 代替外层组件的 onDecrease
         dispatch({type: 'decrease', caption: 'first'})
     }
}
