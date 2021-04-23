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
