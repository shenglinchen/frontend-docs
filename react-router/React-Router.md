# React-Router-dom

[TOC]

## Basic Component

1. 有三种基础组件：路由组件 `router component`，路由匹配组件 `route matching component`，导航组件 `navigation component`
   1. `router component`：`<HashRouter />, <BrowserRouter/ >`。当使用服务器来提供对请求的响应时，应该使用 `<BrowserRouter>`。当使用静态文件时，应该使用 `<HashRouter>`
   2. `route matching component`：`<Route>, <Switch>`
      1. `<Route>` 根据自己的 `path` 属性来匹配路由并渲染内容。如果不匹配则渲染`null`。没有 `path` 的总会被匹配上
      2. `<Switch>` 经常跟`<Route>` group 搭配在一起。`<Switch>` 会遍历所有的`<Route>` 子元素，并渲染第一个被匹配到的`<Route>`
      3. `<Route>` 有三个属性：`component, render, children`，其中经常用的有`component, render`。其中`component` 用于直接传递一个组件：`<Route component={Index} />`。`render`用于使用一个函数进行渲染`<Route render={() => <Index extra={someVariable}}>`，这个时候我们可以传递参数
   3. `navigation component`: `Link, NavLink, Redirect`。`Link` 会渲染成`a`。`NavLink` 是特殊的 `Link` ，在匹配到当前的路由时，将会是 `active`。使用`Redirect` 进行重定向。



## Code Splitting

1. 使用`webpack, @babel/plugin-syntax-dynamic-import, loadable-components`



## Scroll Restoration

1. 在更新的时候回退到页面顶部

   ```jsx
   class ScrollToTop extends Component {
     componentDidUpdate(prevProps) {
       if (this.props.location.pathname !== prevProps.location.pathname) {
         window.scrollTo(0, 0);
       }
     }
   
     render() {
       return this.props.children;
     }
   }
   
   export default withRouter(ScrollToTop);
   
   function App() {
     return (
       <Router>
         <ScrollToTop>
           <App />
         </ScrollToTop>
       </Router>
     );
   }
   ```

   

2. 在加载的时候回到页面顶部

   ```jsx
   class ScrollToTopOnMount extends Component {
     componentDidMount() {
       window.scrollTo(0, 0);
     }
   
     render() {
       return null;
     }
   }
   
   ```

   




## Philosophy

1. 动态路由

   1. 意味着像 `div` 一样进行配置，而不是写在配置里面的
   2. `BroswerRouter, Router, Link`

2. 嵌套路由

   ```jsx
   const App = () => (
     <BrowserRouter>
       {/* here's a div */}
       <div>
         {/* here's a Route */}
         <Route path="/tacos" component={Tacos} />
       </div>
     </BrowserRouter>
   );
   
   // when the url matches `/tacos` this component renders
   const Tacos = ({ match }) => (
     // here's a nested div
     <div>
       {/* here's a nested Route,
           match.url helps us make a relative path */}
       <Route path={match.url + "/carnitas"} component={Carnitas} />
     </div>
   );
   ```

   

3. 

