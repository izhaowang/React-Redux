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
