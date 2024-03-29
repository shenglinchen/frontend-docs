# edux 基础用法

该文档是根据[Redux 中文文档](https://www.redux.org.cn/docs/advanced/Middleware.html)写出。

[TOC]

## 基础概念

`Redux` 提倡将数据储存于唯一的对象树内--`state`，`state` 只能通过发起 `action` 来修改，`action` 即描述发生了什么，这样避免了其他代码随意修改  `state` 而造成的 `bug`。但是 `action` 又不直接修改`state`  ，而是通过 `reducer` 描述 `action ` 是如何修改 `state` 的。并且通过一个大的 `reducer` 来调用多个 `reducer`。

`Redux` 有三个原则：

1. 唯一的 `state`
2. `reducer` 是一个纯函数，即相同的输入有相同的输出
3. `state` 是只读的



## Action

1. `Action` 是一个对象，这个对象中必须有一个属性 `type` ，表示这个 `action` 的动作。其他的属性可以由自己任意确定
2. 同时需要一个函数来作为 `Action 创建函数`，这个函数需要返回一个 `action`
3. `Action` 的触发通过 `store.dispatch(action)` 来触发

## Reducer

1. `Reducer` 必须是一个纯函数

2. `Reducer` 通过判断 `action` 的 `type` 来决定要如何对 `state` 进行修改 

3. 通常将一个大的 `reducer` 拆分成多个小的 `reducer` ，然后将多个小的 `reducer` 组合成一个大的 `reducer`，便于管理

4. 传入`reducer` 的 `state` 不应该被修改，因为 `state` 只读的

5. 注意 `reducer`，应该是一个纯函数，即相同的输入，输出应该是相同的。

6. 每个小的 `reducer` 只管理一部分的 `state`

   ```js
   // 小 reducer
   const addTodo = (state, action) => {
   	...
   }
     
   // 小 reducer
   const filter = (state, action) => {
     ...
   }
     
   // 大 reducer
   const todoApp = (state = {}, action) => {
   	return {
       todos: addTodo(...),
       filter: filter(...)
     }
   }
   
   // 这样一个大的 state 就被切分为多块便于管理
   
   // 另一种合成大 reducer 的方法
   const reducer = combineReducers({
     a: addTodo(...),
     b: filter(...)
   })
   ```

   

7. 到这里我们还无法触发 `reducer` ，需要配合 `store`



## Store

1. `Store` 有一下职责：**Store** 就是把它们联系到一起的对象。Store 有以下职责：
   - 维持应用的 state；
   - 提供 [`getState()`](https://www.redux.org.cn/docs/api/Store.html#getState) 方法获取 state；
   - 提供 [`dispatch(action)`](https://www.redux.org.cn/docs/api/Store.html#dispatch) 方法更新 state；
   - 通过 [`subscribe(listener)`](https://www.redux.org.cn/docs/api/Store.html#subscribe) 注册监听器;
   - 通过 [`subscribe(listener)`](https://www.redux.org.cn/docs/api/Store.html#subscribe) 返回的函数注销监听器
   - 常用的有 `getstate ` 和 `dispatch` 两个
2. 通过 `createStore(reducer, initialState)` 来创建一个 `store`。通过将 `reducer` 传入后，我们就可以在触发 `action` 后触发 `reducer` ，进而修改 `state`
3. 综上我们就已经可以写一个基本的 `redux` 功能了

```ts
// actionTypes.ts
// 在大型应用中，我们经常将 action 的 type 提取为一个单独文件
const ADD_TODOS = 'ADD_TODOS'
const FILTER = 'FILTER'

export default {
  ADD_TODOS,
  FILTER
}
```



```ts
// actions.ts
import types from './actionTypes.ts'

// action 创建函数
export const addTodo = (text: string) => {
  // 返回一个 action
  return {
    type: types.ADD_TODO,
  	todo: text
  }
}

export const filter = {
  ...
}
  

```



```ts
// reducers.ts
import types from './actionTypes.ts'
import {combineReducers} from 'redux'

// 小 reducer
const addTodo = (state = {}, action: any) => {
  switch(action.type) {
    case types.ADD_TODO:
      return {
        ..state,
        action.todo
      }
    default: return {
      ...state
    } 
  }
}

const filter = (state: any, action: any) => {
  switch (action.type){
    case types.FILTER: 
      return ...
  }
}
  
// 合并为一个大 reducer
// 这里我们注意到，传入每个小 reducer 里的并不是整个 state, 而是受该 reducer 管理的那部分
export default todoApp = (state: any, action: any) => {
	return {
    todos: addTodo(state.todo, action),
    filter: filter(state.todo, action)
  }
}

// 也可以用另一种方法
export default todoApp = combineReducers({
  todos: addTodo(state.todo, action),
  filter: filter(state.todo, action)
})
```



```ts
// store.ts
import {createStore} from 'redux'
import {reducer} from './reducer'
// 通过 creacteStore 创建一个 store。
// 回调函数 reducer 会接受两个参数，一个是 state 一个是 action
export default store = createStore(reducer, initialState)
```

 之后我们就可以通过 `store.dispacth(action)` 来修改 `state` 了



## 搭配 React

1. 安装 `react-redux` 
2. 使用 `mapStateToProp` 将 `state` 的一部分绑定到容器组件的 `props` 上
3. 使用 `mapDispatchToProp` 将 `dispatch` 绑定到 `props` 上
4. 完成以上两步需要 `connect` 的协助
```ts
import {connet} from 'redux'

const mapStateToProps = (state: any) => {
  return {
    todos: state.todos
  }
}

const mapDispatchToProps = (dispatch: any) => {
  return {
    addTodo: (text: string) => {
      dispatch(addTodo(text))
    }
  }
}


connect(mapStateToProps, mapDispatchToProps)(组件)

```

4. 之前我们需要手动监听，将 `store` 传递给所有的容器组件。现在我们使用 `react-redux` 提供的 `<Provider>` 来让所有容器组件都能访问

   ```ts
   import {Provider} from 'react-redux'
   import {render} from 'react-dom'
   import store from 'store'
   render(
   	<Provider store={store}>
     	<App />
     </Provider>,
   document.getElementById('app')
   )
   ```

   

5. 


