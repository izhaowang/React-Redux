# React-Redux
react 使用
create-react-app
```
improt React from 'react'
improt ReactDom from 'react-dom';
improt App from './app'; // 根组件
ReactDom.render(
  <App/>,
  document.getElementById('root')
)


// app.js
improt React, {Component} from 'react';

class App extends Component { // 继承react 中的 Component 模块;
  render() {
    return {
      <div></div>
    }
  }
}
export default App;
```


// 声明周期
1.挂载阶段： 把组件挂到DOM树上进行渲染
    constructor // es6 中每个类的构造函数，创建一个组件的实例，会调用构造函数
    ComponentWilMount: 这个时候没有进行渲染； 调用this.setState 不会引发重新绘制
    render react.Component 声明周期都要使用到、唯一的作用返回dom树形结构数据， render 本事不做渲染； 渲染工作是由react完成的
    ComponentDidMount: 这个阶段react 已经根据render函数的东西 进行了渲染；
    
2更新阶段： 数据更新视图也要进行更新（组件重新被渲染）
    ComponentWillReceiveProps： 父组件的render 函数被调用时， 在render 函数中的子组件就会经历更新过程； 注意：this.setState() 这个方法触发的更新不会调用 ComponentWillReceiveProps 函数；  因为  ComponentWillReceiveProps 是会根据新的props 值， 来计算是不是要更新内部的state，也就是是否要调用this.setState() 方法； 
    shoulrComponentUpdate(nextProps， nextSate):  render方法决定了 渲染什么， 而shouldComponentUpdate 决定了 一个组件什么时候不需要渲染；返回boolean 告诉react 是否要继续更新
    componentWillUpdate： 组件更新前 dom 没有gauze
    render： 返回更新后的数据结构
    componentDidUpdate: dom树已经更新
3.卸载阶段： 组件从DOM树上移除 componentWillUnmount


组件如何传递数据
```
1.父组件传递captions 给子组件
// app.js
improt React, {Component} from 'react';
import Counter from './counter.js'// 子组件

class App extends Component { // 继承react 中的 Component 模块;
  render() {
    return {
      <div>
        <Counter captions="first">
          
        </Counter>
      </div>
    }
  }
}
export default App


// counter,js
improt React, {Component} from 'react';


class Counter extends Component { // 继承react 中的 Component 模块;
  render() {
    constrouctor(props) {
      super(props)
      this.state = {
        count: props.caption // 构造函数中通过props获取， 其他函数中通过this.props获取
      }
    }
    return {
      <div>
        <span>{this.count.count}</sapn>
      </div>
    }
  }
}



2. 子组件传递数据给父组件
improt React, {Component} from 'react';
import Counter from './counter.js'// 子组件

class App extends Component { // 继承react 中的 Component 模块;
  onCounterUpdate(newvalue){
    this.setDate({
      counter: newValue
    })
  }
  render() {
    return {
      <div>
        <Counter captions="first" hanlder={this.onCounterUpdate)}> 这里向子组件传递了handler 这个函数
          
        </Counter>
      </div>
    }
  }
}
export default App


// counter,js
improt React, {Component} from 'react';


class Counter extends Component { // 继承react 中的 Component 模块;
  render() {
    constrouctor(props) {
      super(props)
      this.state = {
        count: props.caption // 构造函数中通过props获取， 其他函数中通过this.props获取
      }
    }
    return {
      <div>
        <span onClick={this.props.handler(10)></span> // 点击的时候调用传过来的handler 
      </div>
    }
  }
}
```



如果单纯通过父子组件通信： 那么很容i造成数据重复，不一致； 如果要祖父组件 转递 信息 给子组件， 那么必然让父组件作为桥梁，否则数据传递难度大；
MVC - Flux - Redux 我们直接将Redux 
原理:
1. 用户首先触发Action： 通过store.dispatchi() 传递action 类型
2. 然后stroe 调用Renduce (当前的state, Action); 并且返回一个新的state
3. 如果state发生了改变那么就会调用store.subscribe(this.setState()) 方法 重新渲染 组件


```
const Counter = ({ value, onIncrement, onDecrement }) => (
  <div>
  <h1>{value}</h1>
  <button onClick={onIncrement}>+</button>
  <button onClick={onDecrement}>-</button>
  </div>
);

const reducer = (state = 0, action) => {
  switch (action.type) {
    case 'INCREMENT': return state + 1;
    case 'DECREMENT': return state - 1;
    default: return state;
  }
};

const store = createStore(reducer); //  生成store

const render = () => {
  ReactDOM.render(
    <Counter
      value={store.getState()}
      onIncrement={() => store.dispatch({type: 'INCREMENT'})} // 触发action 后自动调用reduce（） 返回新的state ，
      onDecrement={() => store.dispatch({type: 'DECREMENT'})}
    />,
    document.getElementById('root')
  );
};

render();
store.subscribe(render); // 建通函数

```


