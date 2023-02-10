# react-router-chinese-tutorial

react-router 中文教程，基于 react-router 6.8.1 编写的教程

## 开始

1. 搭建一个 vite + react 的项目

`npm create vite@latest name-of-your-project -- --template react`

2. 打开 name-of-your-project 文件夹，引入 react-router 依赖

`npm install react-router-dom localforage match-sorter sort-by`

3. 运行项目

`npm run dev`

4. 项目搭建运行成功，访问 localhost:5137

![image](https://user-images.githubusercontent.com/48917726/217995568-e66484a0-b3d9-46ab-bdf1-54579c25138c.png)

## 创建一个 router

1. 在 src/main.jsx 中引入 react-router

```javascript
import { createBrowserRouter, RouterProvider } from 'react-router-dom'
```

2. 创建一个 router，这里的 `element` 可以是 jsx ，也可以是 react 组件

```javascript
const router = createBrowserRouter([{
  path: '/',
  element: <div>hello world</div>
}])
```

3. 渲染 router
```javascript
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
```
4. 访问 localhost:5137

![image](https://user-images.githubusercontent.com/48917726/217997109-57f19903-015d-47ff-83c5-abc512795ddb.png)

## 错误处理

默认情况下当你的页面在渲染、加载数据或改变数据时抛出异常，react-router 都会捕获这个异常并渲染一个错误页面。比如说访问 localhost:5137/home，这个路由在 `router` 里匹配不到，所以会显示一个默认的 404 页面

![image](https://user-images.githubusercontent.com/48917726/218019340-24d61f46-a36d-4cb6-9354-d6ffedaced93.png)

### 自定义错误页面

1. 在 src 目录下新建一个 error-page.jsx

2. 引入路由错误 hook
 
```javascript
import { useRouteError } from "react-router-dom";
```

3. 导出组件

```javascript
export default function ErrorPage() {
  const error = useRouteError();
  console.error(error);

  return (
    <div id="error-page">
      <h1>Oops!</h1>
      <p>Sorry, an unexpected error has occurred.</p>
      <p>
        <i>{error.statusText || error.message}</i>
      </p>
    </div>
  );
}
```

3. 在 main.jsx 中引入组件

```javascript
import ErrorPage from "./error-page";
```

4. 在 react-router 中使用

```javascript
const router = createBrowserRouter([{
  path: '/',
  element: <App />,
  errorElement: <ErrorPage />
}])
```

5. 再次访问 localhost:5137/home,会看到显示的是自定义的错误页面，并且在控制台打印出了错误信息

![image](https://user-images.githubusercontent.com/48917726/218024095-6848926c-412e-4527-8f48-1a874d622e96.png)

## 动态路由

假设有一个 userInfo 页面，需要访问 user-info/userId （userId 是不确定的）来显示它，那么我们可以这么写

```javascript
{
  // :something 的写法表示匹配所有字符
  path: "user-info/:userId",
  element: <UserInfo />,
}
```

现在不管是访问 user-info/1,还是 user-info/2，他们都会导航到 userInfo 页面

## 嵌套路由

假设有一个 box 页面，主体是不确定的，头部底部是确定的元素，那么我们就需要用到嵌套路由

1. 首先在 box 页面引入 `<outlet>`

```javascript
import { Outlet } from "react-router-dom";
```

2. 将 `<Outlet />` 放到你需要渲染的位置

```javascript
export default function Box() {
    return <>
        <header>头部</header>
        <main>
            <Outlet />
        </main>
        <footer>底部</footer>
    </>
}
```

3. 把需要嵌套的路由放到目标路由的 children 中，现在访问 /home 或者 /other，Home 或 Other 组件都会渲染到 Box 组件的 `<main>` 元素中

```javascript
{
  path: '/',
  element: <Box />,
  children: [{
    path: "home",
    element: <Home />,
  },{
    path: "other",
    element: <Other />,
  }]
}
```

## 客户端路由

如果你用过 vue-router，那么你就会知道，在 vue-router 中导航到一个路由需要用到 `<router-link>`，那么在 react-router 中有没有类似的组件呢？答案是肯定的，在 react-router 中这个组件是 `<link>`，和 `<outlet>` 一样，如果你要使用 `<link>`，那么你得先引入它

 ```javascript
 import { Link } from "react-router-dom";
 ```
 
 用法和 `<router-link>` 一样
 
 ```javascript
 <Link to={/home}>home</Link>
 ```
 
 `<a>` 标签虽然同样也可以完成路由的导航，但是同时也会发送一个完整的文档请求，而客户端路由则没有这个问题
 
 ![image](https://user-images.githubusercontent.com/48917726/218052552-be1a31ac-0945-476c-b392-b90b79e53620.png)
