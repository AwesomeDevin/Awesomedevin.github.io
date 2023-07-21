---
title: 使用react hooks 构建 redux 进行状态管理
date: 2021-04-20
categories:
- javascript
tags:
- react hooks
---


## 前言
我们在做一个大型的复杂应用时，往往有很多数据会在多个页面多个组件中同时被使用，这时如果仍然使用`props`传参的方式,就会显得组件之间`耦合度过高`，且`开发效率低`。
![](https://user-gold-cdn.xitu.io/2019/12/25/16f3c486e8eae2dc?w=444&h=120&f=png&s=7077)  

为了解决这个问题，2014年 Facebook 提出了 [Flux](https://facebook.github.io/flux/) 架构的概念，引发了很多的实现。2015年，[Redux](https://user-gold-cdn.xitu.io/2019/12/25/16f3c4951200224a) 出现，将 Flux 与函数式编程结合一起，很短时间内就成为了最热门的前端架构。

## 目录
- React Hooks
- Hooks API
- Hooks 构建 Redux
- 完整DEMO


## React Hooks
[hooks](https://reactjs.org/docs/hooks-intro.html) 是react 16.8 引入的特性，他允许你在不写class的情况下操作state 和react的其他特性。  
hooks 只是多了一种写组件的方法，使编写一个组件更简单更方便，同时可以自定义hook把公共的逻辑提取出来，让逻辑在多个组件之间共享。

## Hooks API
#### 基础Hooks
- useState - 声明state变量
```javascript
const { state, setState } = useState(initialState)
```
- useEffect - Effect钩子允许你在函数组件中执行副作用
```javascript
useEffect(didUpdate,[])   //didUpdate为要做的更新,[]为要监听的变量
```
- useContext - 接收一个`Context`对象(`React.createContext`返回的值)并且返回当前`<MyContext.Provider>`的值, 返回值取决于最近的` <MyContext.Provider>`。
```javascript
const  store = useContext(Context)
```

#### 附加Hooks 
- useReducer - 接收一个`(state, action) => newState`类型的reducer,并且返回一个与`dispatch`方法配对的`state`对象(如果你熟悉redux,那么你已经会了)  
```javascript
const [state,dispatch] = useReducer(reducers,initialState)
```
- useCallback - 类似`userEffect`,与前者相比不能执行副作用,返回缓存的函数
- useMemo - 类似`userEffect`,与前者相比不能执行副作用,返回缓存的变量
- useRef - 类似`vue中的$refs`,用于操作dom
- useImperativeHandle - 因为函数式组件不存在组件实例，所以正常情况下ref无法调用子组件的函数，`useImperativeHandle`返回了一个回调函数帮助我们解决此类问题
```javascript
function Com(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
Com = forwardRef(Com);
```
- useLayoutEffect - 类似`componentDidMount/Update, componentWillUnmount`,会在dom元素更新后立即执行回调函数
- useDebugValue - 用于`自定义Hooks`

## Hooks 构建 Redux
Redux 规定，将模型的更新逻辑全部集中于一个特定的层（Flux 里的 store，Redux 里的 reducer）。Flux 和 Redux 都不允许程序直接修改数据，而是用一个叫作 “action” 的普通对象来对更改进行描述。  

需要用到的API有`createContext`,`useContext`,`useReducer`
#### step1 - 首先，我们创建`state`
state.js
```javascript
export default  {
  username:''
}
```
#### step2 - Redux 规定不允许程序直接修改数据，而是用一个叫作 `action` 的普通对象来对state进行更改
action.js
```javascript
export  default {
  setUserName:function(payload){
    return {
      type:'setUserName',
      payload
    }
  }
}
```
#### step3 - 将模型的更新逻辑全部集中于一个特定的层（Flux 里的 store，Redux 里的 `reducer`）
reducer.js
```javascript
export default function reducer(state,action){
  const { payload } = action
  switch(action.type)
  {
    case 'setUserName':     // 匹配action中的type,return新的state
      return {
        ...state, 
        username: payload
      }
    default:
      return state
  }
}
```
#### step4 - 使用`useReducer`获取state以及用于事件派发更新state的`dispatch`函数
index.js
```javascript
const [state,dispatch] = useReducer(reducers,initialState)
```
#### step5 - 允许所有子组件访问state及dispatch
这里我们需要排除`props传参`的方式，因为这样就失去了使用redux进行状态管理的意义，这里就需要用到`Context`  
index.js
```javascript
import React,{createContext, useReducer} from 'react'
import reducers from './reducers'
import initialState from './state'

export const Context = createContext({})
export const Consumer =  Context.Consumer
export function Provider(props){
  const [state,dispatch] = useReducer(reducers,initialState)
  const store = { state, dispatch } 
  return (
  <Context.Provider value={ store }>
    {props.children}
  </Context.Provider>
  )
}
```
这里将`useReducer`的返回值放到`Provider`的value中,子组件通过`Consumer`或`useContext`就能访问到了

整个Redux由`state、action、reducer、index`构成
## 完整DEMO
#### 1. index.js  - 将<App>组件放置在<Provider>中
```
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App';
import { Provider } from './redux'

ReactDOM.render(<Provider><App /></Provider>, document.getElementById('root'))
```
#### 2. app.js - 将<Child>组件放置在<App>中,并更新state
``` 
import React, { useContext } from 'react';
import { AppContext } from './redux'
import Child from './child'
import actions from './redux/actions'

function App() {
  const { dispatch } = useContext( AppContext )   // 获取事件派发函数
  function handleInput(e){
    dispatch(actions.setUserName(e.target.value))   // 派发更新事件并传值
  }
  return (
    <div className="App">
        <input onInput={handleInput} type='text'  />
        <Child  />
    </div>
  );
}
export default App;
```
#### 3. child.js - 获取Redux中的username
```javascript
import React, { useContext } from 'react';
import { AppContext } from './redux'

export default function Children(){
  const { state } = useContext(AppContext)
  return (
    <div>Name：{state.username}</div>
  )
}
```
4. [DEMO源码](https://github.com/AwesomeDevin/blog/tree/master/demo/react-hooks-demo/src)








